<script setup>
    import { ref, reactive } from 'vue';
    import { useUserStore } from '../stores/user';
    import { storeToRefs} from 'pinia';

    const userStore = useUserStore();
    const { errorMessage, loading, user } = storeToRefs(userStore);
    const props = defineProps(['isLogin']);
    const open = ref(false);

    const userCredentials = reactive({
        email: "",
        password: "",
        username: ""
    });

    const showModal = () => {
        open.value = true;
    };

    const clearUserCrendentialsInput = () => {
        userCredentials.email = "";
        userCredentials.password = "";
        userCredentials.username = "";
        userStore.clearErrorMessage()
    }

    const handleOk = async (e) => {
        if(props.isLogin) {
            await userStore.handleLogin({
                password: userCredentials.password,
                email: userCredentials.email
            });
        } else {
            await userStore.handleSignup(userCredentials);
        }
        if(user.value) {
            open.value = false;
            clearUserCrendentialsInput();
        }
    };

    const handleCancel = () => {
        clearUserCrendentialsInput();
        open.value = false;
    };

    const title = props.isLogin ? 'Login' : 'Signup';
</script>

<template>
  <div>
    <a-button type="primary" @click="showModal" class="btn">{{ title }}</a-button>
    <a-modal v-model:open="open" :title="title" @ok="handleOk">
        <template #footer>
            <a-button key="back" @click="handleCancel">Cancel</a-button>
            <a-button :disabled="loading" key="submit" type="primary" :loading="loading" @click="handleOk">Submit</a-button>
        </template>
        <div v-if="!loading" class="input-container">
            <a-input class="input" v-if="!isLogin" v-model:value="userCredentials.username" placeholder="Username" />
            <a-input class="input" v-model:value="userCredentials.email" placeholder="Email" />
            <a-input class="input" v-model:value="userCredentials.password" placeholder="password" type="password" />
        </div>
        <div v-else class="spinner">
            <ASpin />
        </div>
        <ATypographyText v-if="errorMessage" type="danger">{{ errorMessage }}</ATypographyText>
    </a-modal>
  </div>
</template>

<style scoped>
    .btn {
        margin-left: 10px;
    }

    .input {
        margin-top: 5px;
    }

    .input-container {
        height: 120px;
    }

    .spinner {
        display: flex;
        align-items: center;
        justify-content: center;
        height: 120px;
    }
</style>