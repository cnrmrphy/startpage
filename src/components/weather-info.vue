<template>
    <div v-on:click="getWeather">
        <p>Weather:</p>
        <!-- {{ weather.currently.apparentTemperature }} -->
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
            weather: {}
        }
    },
    methods: {
        async getLocation() {
            // const request = await fetch(this.locUrl); 
            // const data = await request.text();
            // console.log(data);
            // return data;
            return ('41.8500,-87.6500');
        },
        async getWeather() {
            let coords = await this.getLocation(); 
            let requestUrl = this.proxyUrl + this.weatherUrl + coords + this.weatherParams;

            const request = await fetch(requestUrl, {
                headers: {
                    "Origin": "startpage.cmurph.me"
                }
            });
            const data = await request.json();
            this.weather = data;
            console.log(data);
            setTimeout(this.getWeather(), 60000);
        }
    },
    // beforeCreated() {
        // this.getWeather();
        // this.interval = setInterval(() => this.getWeather(), 60000);
    // }
}
</script>

<style scoped>

</style>