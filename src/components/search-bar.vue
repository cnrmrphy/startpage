<template>
    <div class="search-container">
        <input type="text" v-model="query">
        <div class="suggestions" v-if="suggestions.length > 0">
            <p v-for="suggestion in suggestions" v-bind:key="suggestion.phrase">{{suggestion.phrase}}</p>
        </div>
    </div>
</template>

<script>
export default {
    name: 'search-bar',
    data: function(){
        return {
            query: '',
            acUrl: 'https://ac.duckduckgo.com/ac/',
            proxyUrl: 'https://cors-anywhere.herokuapp.com/',
            suggestions: [],
        }
    },
    watch: {
        query: async function(){
            if(this.query.length < 1){
                this.suggestions = [];
            }
            const suggUrl = this.acUrl + '?q=' + this.query.split(' ').join('+');
            const res = await fetch(this.proxyUrl + suggUrl);
            const data = await res.json();
            this.suggestions = data;
            console.log(res.url);
            console.log(this.suggestions);
        }
        
    }
}
</script>

<style>

</style>