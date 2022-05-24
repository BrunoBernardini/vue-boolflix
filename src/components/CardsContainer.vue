<template>
  <div class="card-container">
    <h2>{{mediaType}}</h2>
    <div class="card-wrapper">
      <CardItem 
        v-for="(card, index) in mediaList"
        :key="`${card}-${index}`"
        :api_key="api_key"
        :languageToCall="languageToCall"
        :mediaType="mediaType"
        :cardInfo="card"
        :genresIDs="genresIDs"/>
    </div>
  </div>
</template>

<script>
import CardItem from './CardItem.vue';

export default {
  name: "CardContainer",
  components: { CardItem },
  props: {
    api_key: String,
    languageToCall: String,
    mediaType: String,
    mediaList: Array,
    genresIDs: Array
  }
}
</script>

<style lang="scss" scoped>
.card-container{
  position : relative;
  color: white;
  &::before{
    content: "";
    position: absolute;
    z-index: 1;
    top: 0;
    left: 0;
    background-image:
      linear-gradient(to left,
                  rgba(20, 20, 20, 0),
                  rgba(20, 20, 20, 1));
    width: 70px;
    height: 100%;
  }
  &::after{
    content: "";
    position: absolute;
    z-index: 1;
    top: 0;
    right: 0;
    background-image:
      linear-gradient(to right,
                  rgba(20, 20, 20, 0) 0px,
                  rgba(20, 20, 20, 0) 20px,
                  rgba(20, 20, 20, 1));
    width: 90px;
    height: 100%;
  }
  h2,
  .card-wrapper{
    padding: 0 70px;
  }
  h2{
    padding-top: 19px;
    font-weight: bolder;
  }
  .card-wrapper{
    display: flex;
    flex-wrap: nowrap;
    overflow-x: auto;
  }
}
</style>