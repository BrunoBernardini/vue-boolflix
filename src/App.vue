<template>
  <div id="app">
    <HeaderComp @receiveString="searchQuery"/>
    <MainComp :results="results"/>
  </div>
</template>

<script>
import axios from "axios";
import HeaderComp from './components/HeaderComp.vue';
import MainComp from "./components/MainComp.vue";

export default {
  name: 'App',
  components: {
    HeaderComp,
    MainComp
  },
  data(){
    return{
      apiUrl: "https://api.themoviedb.org/3/search/movie",
      apiParams: {
        api_key: "3fc1bcbaae00bbc9201fadced2d28675",
        language: "it-IT",
        query: "",
      },
      results: {
        films: [],
        series: []
      }
    }
  },
  mounted(){
    this.getApi();
  },
  methods: {
    getApi(){
      if(this.apiParams.query.trim() === ""){
        this.results.films = [];
      }
      else{
        axios.get(this.apiUrl, {params: this.apiParams})
          .then(res=>{
            this.results.films = res.data.results;
            console.log(this.results.films);
          })
          .catch(err=>{
            console.log(err);
          })
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
