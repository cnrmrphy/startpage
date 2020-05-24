<template>
    <div class="search-container">
        <input type="text" v-model="query" v-on:keyup.down="increaseActive" v-on:keyup.up="decreaseActive">
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
            suggestions: [],
            activeSuggestion: -1,
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

            let id = 0;
            data.forEach(element => {
                element.id = id;
                id ++;
            });

            this.suggestions = data;
        }
    }
}
</script>

<style>
    .active-suggestion {
        color: blue;
        font-size: 1.2em;
    }
</style>