<template>
  <div id="app">
    <h1>Welcome</h1>
    <SearchBar />
    <LinksWrapper v-bind:links="links"/>
  </div>
</template>

<script>
import LinksWrapper from './components/links-wrapper'
import SearchBar from './components/search-bar'
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
    SearchBar
  }
}
</script>

<style>
  * {
    margin: 0;
    padding: 0;
    font-family: Helvetica, Arial, sans-serif;
  }
</style>
