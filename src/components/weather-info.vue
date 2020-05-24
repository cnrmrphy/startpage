<template>
    <div v-on:click="getLocation">
        weather
    </div>
</template>

<script>
export default {
    name: 'weather-info',
    data: function() {
        return {
            locUrl: 'https://ipinfo.io/loc'
        }
    },
    methods: {
        async getLocation() {
            const request = await fetch(this.locUrl, {
                headers: {
                    'accept': 'application/json',
                    'User-Agent': 'startpage-tab/1.0',
                    'origin': 'startpage.cmurph.me'
                }
            }); 
            const data = await request.json();
            console.log(data);
        }
    }
}
</script>

<style scoped>

</style>