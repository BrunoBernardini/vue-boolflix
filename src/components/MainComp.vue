<template>
  <main>
    <CardsContainer
      mediaType="Film"
      :mediaList="results.films"/>
    <!-- <CardsContainer mediaType="Serie TV"/> -->
  </main>
</template>

<script>
import CardsContainer from './CardsContainer.vue';

export default {
  name: "MainComp",
  components: { 
    CardsContainer
  },
  props: {
    results: Object
  }
}

</script>

<style lang="scss" scoped>

</style>