<template>
  <div>
      <p id="time">{{timeString}}</p>
      <p id="date">{{dateString}}</p>
  </div>
</template>

<script>
export default {
    name: 'date-info',
    data() {
        return {
            dateOpts: {
                weekday: 'long',
                day: 'numeric',
                month: 'long'
            },
            timeOpts: {
               hour: 'numeric',
               minute: 'numeric' 
            },
            timeString: '',
            dateString: '',
        }
    },
    methods: {
        getDate() {
            let clock = new Intl.DateTimeFormat('en-US', this.dateOpts).formatToParts(); 
            let day = clock[0].value;
            let date = clock[4].value.toString();
            let month = clock[2].value;
            this.dateString = day + ', ' + date + ' ' + month;
        },
        getTime() {
            let clock = new Intl.DateTimeFormat('en-US', this.timeOpts).formatToParts();
            let hours = clock[0].value.toString();
            let minutes = clock[2].value.toString();
            this.timeString = hours + ':' + minutes + ' ' + clock[4].value;
            if(this.timeString == '12:00 AM'){
                this.getDate();
            }
        },
    },
    mounted() {
        this.getDate();
        this.getTime();
        this.interval = setInterval(this.getTime, 1000);
    },
    beforeDestroy() {
        clearInterval(this.interval);
    }
}
</script>

<style scoped>
    #time{
        font-size: 1.2em;
    }
</style>