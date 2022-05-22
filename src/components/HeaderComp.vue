<template>
  <header>
    <div class="right-header">
      <a href="#" class="logo">
        <img src="../assets/img/ca3caecece4b4a8cacaf3296fe1c0fc6.png" alt="">
      </a>
      <select class="form-select form-select-sm media-type-select">
        <option selected value="all">All</option>
        <option value="film">Film</option>
        <option value="series">TV Series</option>
      </select>
    </div>
    <div class="left-header">
      <div class="input-group input-group-sm search-bar">
        <button
          class="btn btn-danger"
          type="button"
          @click="$emit('receiveString', titleToSearch)">
          <font-awesome-icon icon="fa-solid fa-magnifying-glass"/>
        </button>
        <input
          type="text"
          class="form-control"
          placeholder="Cerca film e serie TV..."
          v-model="titleToSearch"
          @keyup.enter="$emit('receiveString', titleToSearch)">
      </div>
    </div>
  </header>
</template>

<script>
export default {
  name: "HeaderComp",
  data(){
    return{
      titleToSearch: "",
    }
  }
}
</script>

<style lang="scss" scoped>
@import "../assets/style/vars";
@import "../assets/style/general";

header{
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 70px;
  margin-bottom: 50px;
  width: 100%;
  height: 85px;
  background-color: black;
  .right-header,
  .left-header{
    display: flex;
    align-items: center;
  }
  .logo{
    display: block;
    width: 160px;
    margin-right: 50px;
  }
  .media-type-select{
    width: 160px;
  }
  .search-bar{
    max-width: 200px;
  }
}
</style>