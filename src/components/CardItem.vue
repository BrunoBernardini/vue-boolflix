<template>
  <div
    class="card-item"
    v-if="cardInfo.poster_path || titlesContainer.title || titlesContainer.original_title"
    :class="{'active':active}">
    <div class="card-inner">
      <div 
        class="card-front"
        @click="active = !active;
                getApiCast()">
        <img
          v-if="cardInfo.poster_path"
          :src="`https://image.tmdb.org/t/p/w300/${cardInfo.poster_path}`"
          :alt="titlesContainer.title">
        <h2 v-else>{{titlesContainer.title}}</h2>
      </div>
      <div
        class="card-back"
        @mouseleave="active = !active">
        <h4>{{titlesContainer.title}}</h4>
        <p
          v-if="titlesContainer.title !== titlesContainer.original_title">Titolo originale: <span class="info">{{titlesContainer.original_title}}</span></p>
        <p>
          Genere: <span
                    class="info"
                    v-for="(genreID, index) in genresIDs"
                    :key="`${cardInfo.id}-genre-${genreID}`">
                    <span v-if="cardInfo.genre_ids.includes(genreID.id)">{{genreID.name}}<span v-if="index < (cardInfo.genre_ids.length-1)">, </span></span>
                  </span><br>
          Lingua: <img
                    class="info"
                    :src="`https://flagcdn.com/16x12/${flagID}.png`"
                    :alt="cardInfo.original_language.toUpperCase()"><br>
          Cast: <span
                  class="info"
                  v-for="(castMember, index) in castList"
                  :key="`${castMember.id}-${castMember.cast_id}`">
                  <span v-if="index < 5">{{castMember.name}}<span v-if="index < (castList.length-1)">, </span></span>
                </span><br>
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
    mediaType: String,
    cardInfo: Object,
    genresIDs: Array
  },
  data(){
    return{
      active: false,
      castList: [],
      genresList: []
    }
  },
  methods: {
    getApiCast(){
      if(this.castList.length===0){
        axios.get(`https://api.themoviedb.org/3/${this.movieOrTv}/${this.cardInfo.id}/credits?api_key=${this.api_key}&language=${this.languageToCall}`)
          .then(cast=>{
            this.castList = cast.data.cast;
          })
          .catch(err=>{
            console.log(err);
          })
      }
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
      if(this.mediaType==="Film") return "movie";
      else return "tv";
    },
    titlesContainer(){
      const titlesObject = {
        title: "",
        original_title: ""
      }
      if(this.mediaType==="Film"){
        titlesObject.title = this.cardInfo.title;
        titlesObject.original_title = this.cardInfo.original_title;
      }
      else if(this.mediaType==="Serie TV"){
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
        else if(starsNumber-i >= .5) starsVoteSpecs.half=true;
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
    background: linear-gradient(#bd1621, #56090f);
  }
  .card-front{
    display: flex;
    justify-content: center;
    align-items: center;
    text-align: center;
    overflow: hidden;
    cursor: pointer;
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
      box-shadow: 2px 2px 10px black;
    }
  }
}
</style>