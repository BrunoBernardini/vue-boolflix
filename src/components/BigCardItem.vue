<template>
  <div 
    class="big-card-item"
    @mouseover="getApiCast()">
    <img
      :src="`https://image.tmdb.org/t/p/w1280/${cardInfo.backdrop_path}`"
      :alt="cardInfo.title || cardInfo.name">
    <div class="info-overlay">
      <h3>{{cardInfo.title || cardInfo.name}}</h3>
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
</template>

<script>
import axios from "axios";

export default {
  name: "BigCardItem",
  props: {
    api_key: String,
    languageToCall: String,
    cardInfo: Object,
    genresIDs: Array
  },
  data(){
    return{
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
.big-card-item{
  position: relative;
  width: 95%;
  margin: 30px auto 0 auto;
  text-shadow: 3px 3px 2px black;
  border-radius: 20px;
  outline: 0px solid lightgray;
  transition: all .1s linear;
  overflow: hidden;
  cursor: pointer;
  .info-overlay{
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
    background-image:
      linear-gradient(to left,
                  rgba(0, 0, 0, 0) 0,
                  rgba(0, 0, 0, 0) 50%,
                  rgba(0, 0, 0, .9));
    text-align: start;
    padding: 10px;
    opacity: 0;
    transition: all .4s linear;
    overflow: auto;
    -ms-overflow-style: none;  /* IE and Edge */
    scrollbar-width: none;  /* Firefox */
    h3,
    p{
      width: 50%;
    }
    h3,
    .info{
      color: goldenrod;
    }
    h3{
      font-weight: bolder;
    }
    p{
      font-size: 13px;
    }
    .starsVote{
      position: relative;
      color: yellow;
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
  }
  .info-overlay::-webkit-scrollbar{
    display: none;
  }
  &:hover{
    outline-width: 4px;
    z-index: 999;
  }
  &:hover .info-overlay{
    opacity: 1;
    animation: infoOverlayIn .3s ease-out;
  }
}
@keyframes infoOverlayIn{
  0%{
    left: -30px
  }
  100%{
    left: 0
  }
}
</style>