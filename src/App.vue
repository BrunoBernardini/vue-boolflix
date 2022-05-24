<template>
  <div id="app">
    <HeaderComp 
      @receiveString="searchQuery"
      @receiveFilters="applyFilters"/>
    <CardsContainer
      v-if="filters.movie"
      mediaType="Film"
      :mediaList="medias.movie"/>
    <CardsContainer
      v-if="filters.tv"
      mediaType="Serie TV"
      :mediaList="medias.tv"/>
  </div>
</template>

<script>
import axios from "axios";
import HeaderComp from './components/HeaderComp.vue';
import CardsContainer from "./components/CardsContainer.vue";

export default {
  name: 'App',
  components: {
    HeaderComp,
    CardsContainer
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
      previousQuery: "",
      medias: {
        movie: [],
        tv: []
      },
      filters: {
        movie: true,
        tv: true
      }
    }
  },
  mounted(){
    this.getApi();
  },
  methods: {
    getApi(){
      let actionIndex = 0, afterMedia = "";
      if(this.apiParams.query === "") afterMedia = "/day";
      else actionIndex = 1;
      for(let media in this.medias){
        axios.get(`${this.apiUrl}${this.apiActions[actionIndex]}${media}${afterMedia}?api_key=${this.api_key}`, {params: this.apiParams})
          .then(res=>{
            this.medias[media] = res.data.results;
            console.log(this.medias[media]);
          })
          .catch(err=>{
            console.log(err);
          })
      }
    },
    searchQuery(query){
      this.previousQuery = this.apiParams.query;
      this.apiParams.query = query.trim();
      if(this.previousQuery !== this.apiParams.query) this.getApi();
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
