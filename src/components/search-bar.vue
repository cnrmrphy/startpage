/* eslint disable no-invalid-regexp */
<template>
    <div class="search-container">
        <input autofocus type="text" v-model="query" v-on:keyup.down="increaseActive" v-on:keyup.up="decreaseActive" v-on:click="toggleActive" v-on:keyup.enter="searchHandler">
        <div class="suggestions" v-if="suggestions.length > 0">
            <p v-for="suggestion in suggestions" v-bind:key="suggestion.id" 
                v-bind:class="{activeSuggestion: suggestion.id == activeSuggestion, history: suggestion.type == 'history'}">
                {{suggestion.phrase}}
            </p>
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
            historySuggestions: [],
            apiSuggestions: [],
            activeSuggestion: -1,
            numSuggestions: 8,
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
            let history = JSON.parse(localStorage.getItem('history'));
            if(history && history[query]){
                history[query].num ++;
            } else { 
                let time = new Date();
                time = time.getTime();
                let options = {
                    'time': time,
                    'num': 1
                };
                if(!history){
                    history = {};
                }
                history[query] = options; 
            }
            localStorage.setItem('history', JSON.stringify(history));
        },
        referenceHistory () {
            this.historySuggestions = [];
            let history = JSON.parse(localStorage.getItem('history'));
            if(history){
                let id = 0;
                Object.keys(history).forEach(item => {
                    if(item.toLowerCase().indexOf(this.query.toLowerCase()) != -1){
                        this.historySuggestions.push({
                            'phrase': item,
                            'id': id, 
                            'type': 'history',
                        });
                        id ++;
                    }
                });
            }

            //TODO: sort historysuggestions by num in localhistory 
        },
        async referenceApi () {
            this.apiSuggestions = [];

            const suggUrl = this.acUrl + '?q=' + this.searchableQuery;
            const res = await fetch(this.proxyUrl + suggUrl);
            const data = await res.json();

            let id = this.historySuggestions.length -1;
            data.forEach(element => {
                element.id = id;
                id ++;
                element['type'] = 'web';
            });

            this.apiSuggestions = data;
        } 
    },
    computed: {
        searchableQuery: function() {
            return this.query.split(' ').join('+');
        }
    },
    watch: {
        query: function(){
            this.referenceHistory();
            this.referenceApi();
            this.suggestions = this.historySuggestions.concat(this.apiSuggestions);
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

    .history {
        background-color: beige;
    }
</style>