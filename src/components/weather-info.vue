<template>
    <div>
        <p v-if="weather.currently"> {{ weather.currently.summary }} Feels like {{ Math.round(weather.currently.apparentTemperature) }} F </p>
    </div>
</template>

<script>
export default {
    name: 'weather-info',
    data: function() {
        return {
            locUrl: 'https://ipinfo.io/loc',
            weatherUrl: 'https://darksky.net/forecast/',
            weatherParams: '/us12/en.json',
            proxyUrl: 'https://cors-anywhere.herokuapp.com/',
            coords: '',
            weather: {}
        }
    },
    methods: {
        async getLocation() {
            const request = await fetch(this.locUrl); 
            const data = await request.text();
            // console.log(data);
            this.coords = data;
            // this.coords = '41.8500,-87.6500';
        },
        async getWeather() {
            let requestUrl = this.proxyUrl + this.weatherUrl + this.coords + this.weatherParams;

            const request = await fetch(requestUrl, {
                headers: {
                    "Origin": "startpage.cmurph.me"
                }
            });
            const data = await request.json();
            this.weather = data;
        }
    },
    mounted() {
        this.getLocation();
        this.getWeather();
        this.interval = setInterval(this.getWeather, 900000);
    },
    beforeDestroy() {
        clearInterval(this.interval);
    }
}
</script>

<style scoped>

</style>