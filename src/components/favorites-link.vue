<template>
    <a v-bind:href="link.address">{{ link.name }}</a>
</template>

<script>
export default {
    name: 'favorites-link',
    props: ['link']

}
</script>

<style scoped>
    a{
        padding: 10px;
    }
</style>