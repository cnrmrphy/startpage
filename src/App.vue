<template>
  <div id="app">
    <section id="frame1" class="frame">
      <DateInfo />
      <WeatherInfo />
    </section>
    <SearchBar id="frame2" class="frame"/>
    <LinksWrapper id="frame3" class="frame" v-bind:links="links"/>
  </div>
</template>

<script>
import LinksWrapper from './components/links-wrapper'
import SearchBar from './components/search-bar'
import WeatherInfo from './components/weather-info'
import DateInfo from './components/date-info'
export default {
  name: 'App',
  data: function(){
    return {
      links: [
        {
          id: 0,
          name:'Reddit',
          address:'https://www.reddit.com/',
        },
        {
          id: 1,
          name: 'Gmail',
          address: 'https://mail.google.com/mail/u/0/#inbox',
        },
        {
          id: 2,
          name: 'Youtube',
          address: 'https://www.youtube.com/feed/subscriptions',
        }
      ]
    };
  },
  computed: {
  },
  components: {
    LinksWrapper,
    SearchBar,
    WeatherInfo,
    DateInfo
  }
}
</script>

<style>
  * {
    margin: 0;
    padding: 0;
    font-family: Helvetica, Arial, sans-serif;
  }
  #app{
    display: flex;
    justify-content: center;
    align-items: center;
    flex-direction: column;
    text-align: center;
  }
  .frame{
    border: 5px solid black;
    margin: 15px;
    padding: 20px;
    width: 50%;
  }
  #frame1{
    height: 200px;
    background: url('../public/background.jpg');
  }

  #frame2{
    padding: 0;
  }
</style>
