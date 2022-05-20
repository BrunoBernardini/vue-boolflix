<template>
  <div id="app">
    <HeaderComp/>
  </div>
</template>

<script>
import HeaderComp from './components/HeaderComp.vue';


export default {
  name: 'App',
  components: {
    HeaderComp
}
}
</script>

<style lang="scss">
@import "./assets/style/general";
@import "./assets/style/vars";

#app {
  
}
</style>
