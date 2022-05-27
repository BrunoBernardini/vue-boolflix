<template>
  <div class="big-cards-container">
    <VueSlickCarousel v-bind="settings" v-if="mediaList.length > 0">
      <div
        v-for="(card, index) in shuffledMediaList"
        :key="`bigCardContainer-${card}-${index}`">
        <BigCardItem
          :api_key="api_key"
          :languageToCall="languageToCall"
          :cardInfo="card"
          :genresIDs="genresIDs"/>
      </div>
    </VueSlickCarousel>
  </div>
</template>

<script>
import VueSlickCarousel from 'vue-slick-carousel'
import 'vue-slick-carousel/dist/vue-slick-carousel.css'
// optional style for arrows & dots
import 'vue-slick-carousel/dist/vue-slick-carousel-theme.css'
import BigCardItem from './BigCardItem.vue'

export default {
  name: 'MyComponent',
  components: {
    VueSlickCarousel,
    BigCardItem
  },
  props: {
    api_key: String,
    languageToCall: String,
    mediaList: Array,
    genresIDs: Array
  },
  data(){
    return{
      settings: {
        arrows: true,
        dots: false,
        centerMode: true,
        focusOnSelect: true,
        infinite: true,
        slidesToShow: 3,
        autoplay: true,
        dotsClass: "slick-dots wait"
      }
    }
  },
  computed: {
    shuffledMediaList(){
      let shuffledMediaListArray = this.mediaList;
      return shuffledMediaListArray.sort(()=>Math.random()-0.5);
    }
  }
}
</script>

<style lang="scss" scoped>
.big-cards-container{
  color: white;
  text-align: center;
  padding: 0px 40px 30px 40px;
  position: relative;
  &::before{
    content: "";
    position: absolute;
    z-index: 1;
    top: 0;
    left: 40px;
    background-image:
      linear-gradient(to left,
                  rgba(20, 20, 20, 0),
                  rgba(20, 20, 20, 1));
    width: 40px;
    height: 100%;
  }
  &::after{
    content: "";
    position: absolute;
    z-index: 1;
    top: 0;
    right: 40px;
    background-image:
      linear-gradient(to right,
                  rgba(20, 20, 20, 0),
                  rgba(20, 20, 20, 1));
    width: 40px;
    height: 100%;
  }
}
@media screen and (max-width: 1100px) {
  .big-cards-container{
    display: none;
  }
}
</style>