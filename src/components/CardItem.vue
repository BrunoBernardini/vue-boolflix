<template>
  <div
    class="card-item"
    v-if="cardInfo.poster_path || cardInfo.title || cardInfo.original_title"
    :class="{'active':active}">
    <div class="card-inner">
      <div 
        class="card-front"
        @click="active = !active">
        <img
          v-if="cardInfo.poster_path"
          :src="`https://image.tmdb.org/t/p/w300/${cardInfo.poster_path}`"
          :alt="cardInfo.title">
        <h2 v-else>{{cardInfo.title}}</h2>
      </div>
      <div
        class="card-back"
        @mouseleave="active = !active">
        <h4>{{cardInfo.title}}</h4>
        <p>Titolo originale: <span class="info">{{cardInfo.original_title}}</span></p>
        <p>Lingua: <img
                    class="info"
                    :src="`https://flagcdn.com/16x12/${flagID}.png`"
                    :alt="cardInfo.original_language.toUpperCase()"><br>
           Punteggio: <span class="info">{{cardInfo.vote_average}}</span> [{{cardInfo.vote_count}} voti]</p>
        <p class="plot">Trama:<br>{{cardInfo.overview}}</p>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: "CardItem",
  props: {
    cardInfo: Object
  },
  data(){
    return{
      active: false
    }
  },
  computed: {
    flagID(){
      const flagAlias = {
        en: "gb",
      }
      if(this.cardInfo.original_language in flagAlias) return flagAlias[this.cardInfo.original_language];
      else return this.cardInfo.original_language;
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
    text-decoration: underline;
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