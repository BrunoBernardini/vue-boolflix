<template>
  <div id="app">
    <HeaderComp 
      @receiveString="searchQuery"
      @receiveFilters="applyFilters"/>
    <BigCardsContainer
      v-if="apiParams.query == '' && ((filters.movie && medias.movie.length > 0)&&(filters.tv && medias.tv.length > 0))"
      :api_key="api_key"
      :languageToCall="apiParams.language"
      :mediaList="topTrendings"
      :genresIDs="genresLists.movie.concat(genresLists.tv)"/>
    <CardsContainer
      v-if="filters.movie && medias.movie.length > 0"
      :api_key="api_key"
      :languageToCall="apiParams.language"
      mediaType="Film"
      :mediaList="medias.movie"
      :genresIDs="genresLists.movie"/>
    <CardsContainer
      v-if="filters.tv && medias.tv.length > 0"
      :api_key="api_key"
      :languageToCall="apiParams.language"
      mediaType="Serie TV"
      :mediaList="medias.tv"
      :genresIDs="genresLists.tv"/>
  </div>
</template>

<script>
import axios from "axios";
import HeaderComp from './components/HeaderComp.vue';
import CardsContainer from "./components/CardsContainer.vue";
import BigCardsContainer from "./components/BigCardsContainer.vue";

export default {
  name: 'App',
  components: {
    HeaderComp,
    CardsContainer,
    BigCardsContainer
},
  data(){
    return{
      apiUrl: "https://api.themoviedb.org/3/",
      apiActions: [
        "trending/",
        "search/"
      ],
      api_key: "3fc1bcbaae00bbc9201fadced2d28675",
      apiParams: {
        language: "it-IT",
        query: "",
      },
      trendingTypeWindow: "day",
      previousQuery: "",
      medias: {
        movie: [],
        tv: []
      },
      topTrendings: [],
      filters: {
        movie: true,
        tv: true
      },
      genresLists: {
        movie: [],
        tv: []
      },
      fullGenresList: []
    }
  },
  mounted(){
    this.getApiMoviesAndTv();
    this.getApiGenres();
  },
  methods: {
    getApiMoviesAndTv(){
      let actionIndex = 0, afterMedia = "";
      if(this.apiParams.query === "") afterMedia = `/${this.trendingTypeWindow}`;
      else actionIndex = 1;
      this.topTrendings = [];
      for(let media in this.medias){
        axios.get(`${this.apiUrl}${this.apiActions[actionIndex]}${media}${afterMedia}?api_key=${this.api_key}`, {params: this.apiParams})
          .then(res=>{
            this.medias[media] = res.data.results;
            for(let i=0; i<Math.min(10,this.medias[media].length); i++) this.topTrendings.push(this.medias[media][i]);
          })
          .catch(err=>{
            console.log(err);
          })
      }
    },
    getApiGenres(){
      for(let listGenreType in this.genresLists){
        axios.get(`https://api.themoviedb.org/3/genre/${listGenreType}/list?api_key=${this.api_key}&language=${this.apiParams.language}`)
          .then(genres=>{
            this.genresLists[listGenreType] = genres.data.genres;
          })
          .catch(err=>{
            console.log(err);
          })
      }
    },
    searchQuery(query){
      this.previousQuery = this.apiParams.query;
      this.apiParams.query = query.trim();
      if(this.previousQuery !== this.apiParams.query) this.getApiMoviesAndTv();
    },
    applyFilters(filtersObject){
      this.filters = filtersObject;
    }
  }
}
</script>

<style lang="scss">
@import "./assets/style/vars";
@import "./assets/style/general";
#app {
  min-height: 100vh;
  background-color: rgb(20, 20, 20);
}
</style>
