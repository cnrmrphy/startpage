/* eslint disable no-invalid-regexp */
<template>
    <div class="search-container">
        <input autofocus type="text" v-model="query" v-on:keyup.down="increaseActive" v-on:keyup.up="decreaseActive" v-on:click="toggleActive" v-on:keyup.enter="searchHandler">
        <div class="suggestions" v-if="suggestions.length > 0">
            <p v-for="suggestion in suggestions" v-bind:key="suggestion.id" v-bind:class='{"active-suggestion": suggestion.id == activeSuggestion}'>{{suggestion.phrase}}</p>
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
            searchUrl: 'https://www.google.com/search?q=',
            suggestions: [],
            activeSuggestion: -1,
            urlRegex: /^(([\w-]+?:\/\/)?[\w-]+(\.[\w-]+)+\.?(:\d+)?(\/\S*)?)$/,
            protocolRegex: /^http(s):\/\//
        }
    },
    methods: {
        decreaseActive () {
            if(this.activeSuggestion <= 0){
                this.activeSuggestion = this.suggestions.length - 1;
            } else if(this.activeSuggestion > 0){
                this.activeSuggestion --;
            }
        },
        increaseActive () {
            if(this.activeSuggestion < 0){
                this.activeSuggestion = 0;
            } else if (this.activeSuggestion < this.suggestions.length -1){
                this.activeSuggestion ++;
            } else {
                this.activeSuggestion = 0;
            }
        },
        toggleActive () {
            if(this.activeSuggestion >= 0){
                this.activeSuggestion = -1;
            }
        },
        searchHandler () {
            this.storeQuery(this.activeSuggestion < 0 ? this.query : this.suggestions[this.activeSuggestion].phrase);
            if(this.activeSuggestion < 0){
                let link = this.query
                if(this.urlRegex.test(link)){
                    if(!this.protocolRegex.test(link)){
                        link = 'http://' + link;
                    }
                    window.location.assign(link);
                } else {
                    window.location.assign(this.searchUrl + this.searchableQuery);
                }
            } else {
                let link = this.suggestions[this.activeSuggestion].phrase;
                if(this.urlRegex.test(link)){
                    if(!this.protocolRegex.test(link)){
                        link = 'http://' + link;
                    }
                    window.location.assign(link);
                } else {
                    window.location.assign(this.searchUrl + link);
                } 
            }
            this.query = '';
        },
        storeQuery (query) {
            let queryData = JSON.parse(localStorage.getItem(query));
            if(queryData){
                queryData.num ++;
                localStorage.setItem(query, JSON.stringify(queryData));
            } else { 
                let time = new Date();
                time = time.getTime();
                let options = {
                    'time': time,
                    'num': 1
                }
                localStorage.setItem(query, JSON.stringify(options));
            }
            
        }
    },
    computed: {
        searchableQuery: function() {
            return this.query.split(' ').join('+');
        }
    },
    watch: {
        query: async function(){
            if(this.query.length < 1){
                this.suggestions = [];
            }
            const suggUrl = this.acUrl + '?q=' + this.searchableQuery;
            const res = await fetch(this.proxyUrl + suggUrl);
            const data = await res.json();

            let id = 0;
            data.forEach(element => {
                element.id = id;
                id ++;
            });

            this.suggestions = data;
        },
        // TODO: maybe figure out how to implement something like this to fully replicate chrome's bar
        // right now the problem is that it activates the above and corrupts the current list by running the
        // api on each guy that it runs through 
        // activeSuggestion: function(){
        //     if(this.activeSuggestion >= 0){
        //         this.query = this.suggestions[this.activeSuggestion].phrase;
        //     }
        // }
    }
}
</script>

<style scoped>
    .active-suggestion {
        color: blue;
        font-size: 1.2em;
    }

    input{
        border-radius: 0;
    }
    input:focus{
        outline: none;
    }
</style>