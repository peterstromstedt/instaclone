## installed extra ##
npm install ant-design-vue
##

we are utilizing supabase.com
install the supabase package
npm install @supabase/supabase-js


<script setup>
    import Profile from '../components/Profile.vue'
</script>

<template>
    <main>
       <Profile :key="$route.params.username"/>
    </main>
</template>


the :key="$route.params.username" triggers the re render of the site. which was added to the Profile.vue userbar rendering.
added this to the profileview when we render the profile


we are using Intersection Observer API for scrollbased pagination?
(which is built into javascript)
can read more on an mdn page.