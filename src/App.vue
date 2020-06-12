<template>
  <div id="wrapper" v-bind:style="{height: calcHeight}">
    <div id="app">
      <section id="frame1" class="frame">
        <section id="inset" class="frame">
          <DateInfo />
          <WeatherInfo />
        </section>
      </section>
      <SearchBar id="frame2" class="frame"/>
      <LinksWrapper id="frame3" class="frame" v-bind:links="links"/>
    </div>
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
    calcHeight() {
      if(window.outerHeight != screen.availHeight){
        return '100%';
      } else {
        const totalHeight = screen.availHeight;
        console.log('total size of monitor: ' + totalHeight);
        const windowHeight = window.innerHeight;
        console.log('size of available browser space: ' + windowHeight);
        const taskBar = totalHeight - windowHeight;
        console.log('size of the browser top taskbar: ' + taskBar);
        const paddedHeight = windowHeight - taskBar;
        console.log('padded maximum size of the window: ' + paddedHeight);
        return paddedHeight + 'px';
      }
    }
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
    box-sizing:border-box;
  }
  #app{
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    height: 100%;
  }
  .frame{
    border: 5px solid black;
    margin: 15px;
    padding: 20px;
    width: 50%;
    background-color:white;
  }
  #inset{
    margin:0;
    padding:10px;
    background-color:white;
    float:right;
    text-align:right;
    line-height:1.4;
  }
  #frame1{
    height: 200px;
    background: url('../public/background.jpg');
  }

  #frame2{
    padding: 0;
  }
</style>
