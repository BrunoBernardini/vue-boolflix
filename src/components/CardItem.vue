<template>
  <div
    class="card-item"
    v-if="cardInfo.poster_path || titlesContainer.title || titlesContainer.original_title"
    :class="{'active':active}">
    <div class="card-inner">
      <div 
        class="card-front"
        @click="active = true;
                getApiCast()">
        <img
          v-if="cardInfo.poster_path"
          :src="`https://image.tmdb.org/t/p/w300/${cardInfo.poster_path}`"
          :alt="titlesContainer.title">
        <h2 v-else>{{titlesContainer.title}}</h2>
      </div>
      <div
        class="card-back"
        @click="active = false"
        @mouseleave="active = false">
        <h4>{{titlesContainer.title}}</h4>
        <p
          v-if="titlesContainer.title !== titlesContainer.original_title">Titolo originale: <span class="info">{{titlesContainer.original_title}}</span></p>
        <p>
          Genere: <span
                    class="info"
                    v-for="(genre_id, index) in cardInfo.genre_ids"
                    :key="`${cardInfo.id}-genre-${genre_id}`">
                    {{matchGenre(genre_id, genresIDs)}}<span v-if="index < (cardInfo.genre_ids.length-1)">, </span>
                  </span><br>
          Lingua: <img
                    class="info"
                    :src="`https://flagcdn.com/16x12/${flagID}.png`"
                    :alt="cardInfo.original_language.toUpperCase()"><br>
          Cast: <span
                  class="info"
                  v-for="(castMember, index) in castList"
                  :key="`${castMember.id}-${castMember.cast_id}`">
                  <span v-if="index < 5">{{castMember.name}}<span v-if="index < Math.min((castList.length-1),4)">, </span></span>
                </span>
                <span
                  v-if="castList.length === 0"
                  class="info">N/A</span><br>
          Punteggio: <span class="starsVote">
                        <font-awesome-icon 
                          v-for="nFull in starsVote.full"
                          :key="`fullStar-${nFull}`"
                          icon="fa-solid fa-star"/>
                        <font-awesome-icon
                          v-if="starsVote.half"
                          icon="fa-solid fa-star-half-stroke"/>
                        <font-awesome-icon
                          v-for="nEmpty in starsVote.empty"
                          :key="`emptyStar-${nEmpty}`"
                          icon="fa-regular fa-star"/>
                        <div class="starsInfo">{{cardInfo.vote_average/2}}/5 [{{cardInfo.vote_count}} voti]</div>
                      </span>
        </p>
        <p
          class="plot"
          v-if="cardInfo.overview">Trama:<br>{{cardInfo.overview}}</p>
      </div>
    </div>
  </div>
</template>

<!-- <span class="info">{{cardInfo.vote_average}}</span> [{{cardInfo.vote_count}} voti] -->

<script>
import axios from "axios";

export default {
  name: "CardItem",
  props: {
    api_key: String,
    languageToCall: String,
    cardInfo: Object,
    genresIDs: Array
  },
  data(){
    return{
      active: false,
      castList: [],
      previousID: null
    }
  },
  methods: {
    getApiCast(){
      if(this.previousID !== this.cardInfo.id){
        this.previousID = this.cardInfo.id;
        axios.get(`https://api.themoviedb.org/3/${this.movieOrTv}/${this.cardInfo.id}/credits?api_key=${this.api_key}&language=${this.languageToCall}`)
          .then(cast=>{
            this.castList = cast.data.cast;
            console.log(this.previousID);
          })
          .catch(err=>{
            console.log(err);
          })
      }
    },
    matchGenre(genreID, genresList){
      let genreName = "";
      for(let genreObject of genresList){
        if(genreObject.id===genreID) genreName = genreObject.name;
      }
      return genreName;
    }
  },
  computed: {
    flagID(){
      const flagAlias = {
        en: "gb",
        ja: "jp",
      }
      if(this.cardInfo.original_language in flagAlias) return flagAlias[this.cardInfo.original_language];
      else return this.cardInfo.original_language;
    },
    movieOrTv(){
      if('title' in this.cardInfo) return "movie";
      else return "tv";
    },
    titlesContainer(){
      const titlesObject = {
        title: "",
        original_title: ""
      }
      if('title' in this.cardInfo){
        titlesObject.title = this.cardInfo.title;
        titlesObject.original_title = this.cardInfo.original_title;
      }
      else if('name' in this.cardInfo){
        titlesObject.title = this.cardInfo.name;
        titlesObject.original_title = this.cardInfo.original_name;
      }
      return titlesObject;
    },
    starsVote(){
      const starsNumber = this.cardInfo.vote_average/2;
      const starsVoteSpecs = {
        full: 0,
        empty: 0,
        half: false
      };
      for(let i=0; i<5; i++){
        if(i+1<starsNumber || starsNumber-i > .85) starsVoteSpecs.full++;
        else if(starsNumber-i >= .3) starsVoteSpecs.half=true;
        else starsVoteSpecs.empty++;
      }
      return starsVoteSpecs;
    }
  }
}
</script>

<style lang="scss" scoped>
.card-item{
  flex: 0 0 auto;
  width: 210px;
  height: 315px;
  margin: 20px 10px 20px 0;
  background-color: transparent;
  perspective: 1000px;
  h4,
  p{
    margin-bottom: 10px;
  }
  h4,
  .info{
    color: goldenrod;
  }
  h4,
  .card-front{
    color: goldenrod;
    text-shadow: 3px 3px 3px black;
    font-weight: bolder;
    h2{
      font-weight: bolder;
    }
  }
  p{
    font-size: 12px;
  }
  .info{
    font-weight: bolder;
  }
  .starsVote{
    position: relative;
    color: yellow;
    text-shadow: 3px 3px 3px black;
    cursor: pointer;
    display: inline-block;
    .starsInfo{
      position: absolute;
      bottom: -20px;
      left: 10px;
      width: max-content;
      background-color: rgba(0, 0, 0, .5);
      text-shadow: none;
      padding: 1px 10px;
      border-radius: 5px;
      display: none;
    }
    &:hover .starsInfo{
      display: block;
    }
  }
  .card-inner{
    position: relative;
    width: 100%;
    height: 100%;
    transition: transform .8s;
    transform-style: preserve-3d;
  }
  &.active .card-inner{
    transform: rotateY(180deg);
  }
  .card-front, .card-back{
    position: absolute;
    width: 100%;
    height: 100%;
    -webkit-backface-visibility: hidden; /* Safari */
    backface-visibility: hidden;  
    border-radius: 10px;
    word-break: break-word;
    cursor: pointer;
    background: linear-gradient(#bd1621, #56090f);
  }
  .card-front{
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    overflow: hidden;
    h2{
      padding: 10px;
    }
    img{
      height: 100%;
      width: 100%;
      object-fit: cover;
    }
  }
  .card-back{
    padding: 10px 15px 0 10px;
    color: wheat;
    overflow: auto;
    transform: rotateY(180deg);
    img{
      margin-left: 3px;
      box-shadow: 2px 2px 10px black;
    }
  }
}
</style>