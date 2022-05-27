<template>
  <header>
    <div class="right-header">
      <a 
        href="#"
        class="logo"
        @click="$emit('receiveString', ''); titleToSearch = ''">
        <img src="../assets/img/ca3caecece4b4a8cacaf3296fe1c0fc6.png" alt="">
      </a>
      <select
        class="form-select form-select-sm media-type-select"
        @change="sendFilters">
        <option selected value="">Film e serie TV</option>
        <option value="film">Film</option>
        <option value="series">Serie TV</option>
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
          :placeholder="`Cerca ${whatToSearch}`"
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
      visibility: {
        movie: true,
        tv: true
      }
    }
  },
  methods: {
    sendFilters(filterOption){
      if(filterOption.target.value === "film"){
        this.visibility.movie = true;
        this.visibility.tv = false;
      }
      else if(filterOption.target.value === "series"){
        this.visibility.movie = false;
        this.visibility.tv = true;
      }
      else{
        this.visibility.movie = true;
        this.visibility.tv = true;
      }
      this.$emit("receiveFilters", this.visibility);
    }
  },
  computed: {
    whatToSearch(){
      let whatToSearchString;
      if(this.visibility.movie && this.visibility.tv) whatToSearchString = "film e serie TV";
      else if(this.visibility.movie) whatToSearchString = "film";
      else whatToSearchString = "serie TV";
      return whatToSearchString += "...";
    }
  }
}
</script>

<style lang="scss" scoped>
header{
  display: flex;
  flex-wrap: wrap;
  justify-content: space-between;
  align-items: center;
  padding: 0 70px;
  margin-bottom: 20px;
  width: 100%;
  min-height: 85px;
  background-color: black;
  .right-header,
  .left-header{
    display: flex;
    flex-wrap: wrap;
    align-items: center;
    a,
    select,
    div{
      margin-top: 5px;
      margin-bottom: 5px;
    }
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