/* eslint disable no-invalid-regexp */
<template>
    <div class="search-container" v-on-clickaway="hideList">
        <div class="text-container">
            <input id="omnibox" autofocus type="text" placeholder="search" v-model="query" 
                v-on:keyup.down="increaseActive" v-on:keyup.up="decreaseActive" v-on:click="resetActive" v-on:keyup.enter="searchHandler"
                >
        </div>
        <div class="suggestions" v-if="showList" v-bind:style="suggestions.length > 0 ? 'display:block' : 'display:none'">
            <p v-for="suggestion in suggestions" v-on:click="followSuggestion(suggestion.id)" v-bind:key="suggestion.id" 
                v-bind:class="{'active-suggestion': suggestion.id == activeSuggestion, history: suggestion.type == 'history'}"
                >
                {{suggestion.phrase}}
            </p> 
        </div>
    </div>
</template>

<script>
import { directive as onClickaway } from 'vue-clickaway';
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
            protocolRegex: /^http(s):\/\//,
            showList: true,
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
        resetActive () {
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
        followSuggestion(id){
            this.activeSuggestion = id;
            this.searchHandler();
        },
        storeQuery (query) {
            let history = JSON.parse(localStorage.getItem('history'));
            let time = new Date();
            time = time.getTime();

            if(history && history[query]){
                history[query].num ++;
                history[query].time = time;
            } else { 
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
                    if(item.toLowerCase().startsWith(this.query.toLowerCase())){
                        this.historySuggestions.push({
                            'phrase': item,
                            'id': id, 
                            'type': 'history',
                        });
                        id ++;
                    }
                });
            }
            //todo: sort historysuggestions by num in localhistory 
        },
        async referenceApi () {
            this.apiSuggestions = [];
            const suggUrl = this.acUrl + '?q=' + this.searchableQuery;
            try {
                const res = await fetch(this.proxyUrl + suggUrl);
                const data = await res.json();

                let id = this.historySuggestions.length;
                data.forEach(element => {
                    element.id = id;
                    element.type = 'web';
                    id++;
                });
                //TODO: Filter data so that it does not contain duplicates with history suggestions
                //somewhat difficult i guess because it's a dictionary
                this.apiSuggestions = data.slice(0, this.numSuggestions - this.historySuggestions.length);
            } catch(e) {
                console.log(e);
            }
        },
        hideList() {
            console.log('gonna hide the brother now');
            this.showList = false;
        }
        
    },
    computed: {
        searchableQuery: function() {
            return this.query.split(' ').join('+');
        }
    },
    watch: {
        query: async function(){
            // this.paddedQuery = '';
            this.activeSuggestion = -1;
            if(this.query.length < 1){
                this.suggestions = [];
            } else {
                this.referenceHistory();
                await this.referenceApi();
                this.suggestions = this.historySuggestions.concat(this.apiSuggestions);
            }
            // if(this.suggestions.length > 0){
                // this.paddedQuery = this.suggestions[0].phrase;
            // }
            this.showList = true;
        },
        // todo: maybe figure out how to implement something like this to fully replicate chrome's bar
        // right now the problem is that it activates the above and corrupts the current list by running the
        // api on each guy that it runs through 
        // activeSuggestion: function(){
        //     // if(this.activeSggestion >= 0){
        //         document.getElementById('omnibox').value = this.suggestions[this.activeSuggestion].phrase;
        //     // }
        // }
    },
    directives: {
        onClickaway,
    }
}
</script>

<style scoped>
    .search-container {
        position: relative;
    }
    .text-container {
        overflow-x: hidden;
    }
    .active-suggestion {
        color: #bd93f9;
    }
    .suggestions{
        z-index: 1;
        position: absolute;
        left: 50%;
        transform: translate(-50%, 0);
        width: 100%;
        background-color: white;
        border: 5px solid black;
        box-sizing:content-box;
    }
    .suggestions p{
        padding-left:5%;
    }
    input{
        outline:none;
        border:none;
        width: 100%;
        line-height:3em;
        padding-left:5%;
    }
</style>