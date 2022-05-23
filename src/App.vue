<template>
  <div id="app">
    <HeaderComp @receiveString="searchQuery"/>
    <CardsContainer
      mediaType="Film"
      :mediaList="medias.movie"/>
    <CardsContainer
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
      apiUrl: "https://api.themoviedb.org/3/search/",
      apiMediaSearch: [
        "movie",
        "tv"
      ],
      apiParams: {
        api_key: "3fc1bcbaae00bbc9201fadced2d28675",
        language: "it-IT",
        query: "",
      },
      medias: {
        movie: [],
        tv: []
      }
    }
  },
  mounted(){
    this.getApi();
  },
  methods: {
    getApi(){
      if(this.apiParams.query.trim() === ""){
        this.medias.movie = [];
      }
      else{
        for(let media in this.medias){
          axios.get(`${this.apiUrl}${media}`, {params: this.apiParams})
            .then(res=>{
              this.medias[media] = res.data.results;
              console.log(this.medias[media]);
            })
            .catch(err=>{
              console.log(err);
            })
        }
      }
    },
    searchQuery(query){
      this.apiParams.query = query;
      this.getApi();
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
