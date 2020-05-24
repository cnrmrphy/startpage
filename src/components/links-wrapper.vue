<template>
    <div class="container">
        <favoritesLink v-for="link in links" v-bind:key="link.id" v-bind:link="link"/>
    </div>
</template>

<script>
import favoritesLink from './favorites-link';
export default {
    name: 'links-wrapper',
    props: ['links'],
    components: {
        favoritesLink,
    }
}
</script>

<style scoped>
   .container{
       display: flex;
   } 
</style>