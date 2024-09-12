<script setup>
    import { ref } from 'vue';
    import { supabase } from '../supabase';
    import { useUserStore } from '../stores/user';
    import { storeToRefs } from 'pinia';

    const userStore = useUserStore();

    const props = defineProps(['addNewPost']);
    const { user } = storeToRefs(userStore);
    const errorMessage = ref("");
    const loading = ref(false);
    const open = ref(false);
    const caption = ref("");
    const file = ref(null);

    const showModal = () => {
        open.value = true;
    };

    const handleOk = async () => {  
        loading.value = true;
        const fileName = Math.floor(Math.random() * 1000000000000);
        let filePath;
        if(file.value) {
            const { data, error } = await supabase.storage.from('images').upload('public/' + fileName, file.value);

            if(error) {
                loading.value = false;
                return errorMessage.value = "Unable to upload image";
            }

            filePath = data.path;
            await supabase.from("posts").insert({
                url: filePath,
                caption: caption.value,
                user_id: user.value.id
            });
        }
        loading.value = false;
        open.value = false;
        caption.value = "";
        props.addNewPost({
            url: filePath,
            caption: caption.value
        });
    };

    const handleUploadChange = (e) => {
        if(e.target.files[0]) {
            file.value = e.target.files[0];
        }
    };
</script>

<template>
    <div>
        <a-button @click="showModal">Upload Photo</a-button>
        <a-modal v-model:open="open" title="Upload Photo" @ok="handleOk">
            <div v-if="!loading">
                <input type="file" accept=".jpeg, .png, .jpg" @change="handleUploadChange">
                <AInput placeholder="Caption.." v-model:value="caption" :maxLength="50" />
                <ATypographyText v-if="errorMessage" type="danger">{{ errorMessage }}</ATypographyText>
            </div>
            <div class="spinner" v-else>
                <ASpin />
            </div>
            
        </a-modal>
    </div>
</template>

<style scoped>
    input {
        margin-top: 10px;
    }
    
    .spinner {
        display: flex;
        align-items: center;
        justify-content: center;
    }
</style>