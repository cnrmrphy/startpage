<template>
    <div>
        <p> {{ conditions }}–Feels like {{ temp }} F </p>
    </div>
</template>

<script>
export default {
    name: 'weather-info',
    data: function() {
        return {
            locUrl: 'https://ipinfo.io/loc',
            apiUrl: 'http://free.ipwhois.io/json/?objects=latitude,longitude',
            weatherUrl: 'https://api.openweathermap.org/data/2.5/weather',
            lat: '',
            lon: '',
            weather: {},
            temp: '',
            conditions: ''
        }
    },
    methods: {
        async getLocation() {
            const request = await fetch(this.apiUrl); 
            const data = await request.text();
            // var data = '41.8500,-87.6500'; 
            this.lat = parseInt(data.split(',')[0]).toFixed(2);
            this.lon = parseInt(data.split(',')[1]).toFixed(2);
            // this.lat = parseInt(response.latitude).toFixed(2);
            // this.lon = parseInt(response.longitude).toFixed(2);
            console.log('lat/long: ' + this.lat + '/' + this.lon);
        },
        async getWeather() {
            const VUE_APP_WEATHER_KEY = process.env.VUE_APP_WEATHER_KEY;
           
            let requestUrl = this.weatherUrl + '?lat=' + this.lat + '&lon=' + this.lon + '&appid=' + VUE_APP_WEATHER_KEY;
            const request = await fetch(requestUrl, {
                headers: {
                    "origin": "startpage.cmurph.me",
                }
            });
            const data = await request.json();
            console.log(data);
            this.weather = data;
            this.handleWeather(this.weather);
        },
        toFahrenheit(kelvin) {
            var temp = (kelvin - 273.15) * (9/5) + 32;
            return Math.round(temp);
        },
        handleWeather(weather) {
            this.temp = this.toFahrenheit(weather.main.feels_like);
            this.conditions = this.titleCase(weather.weather[0].description);
        },
        titleCase(string) {
            let words = string.split(' ');
            let sentence = [];
            words.forEach(word => {
                let title = word[0].toUpperCase();
                sentence.push(title + word.slice(1));
            });
            return sentence.join(' ');
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