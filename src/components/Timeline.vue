<script setup>    
    import Cards from './Cards.vue';
    import LogInMessage from './LogInMessage.vue';
    import Container from './Container.vue';
    import { useUserStore } from '../stores/user';
    import { storeToRefs } from 'pinia';

    const userStore = useUserStore();

    const { user, loadingUser } = storeToRefs(userStore); 
</script>

<template>
    <Container>
        <div v-if="!loadingUser">
            <Cards v-if="user"/>
            <LogInMessage v-else />
        </div>
        <div class="timeline-spinner" v-else>
            <ASpin />
        </div>
    </Container>
</template>

<style>
    .timeline-container {
        display: flex;
        flex-direction: column;
        align-items: center;
        padding: 20px 0;
    }

    .timeline-spinner {
        display: flex;
        align-items: center;
        justify-content: center;
        height: 90vh;
    }
</style>