<template>
  <NavBar @searchCoin="search" @resetSearch="searchText = ''"/>
  <CryptoContent :searchText="searchText"/>
</template>

<script>
import NavBar from './components/NavBar.vue'
import CryptoContent from './components/CryptoContent.vue'

export default {
  name: 'App',
  components: {
    NavBar,
    CryptoContent,
  },
  data(){
    return {
      searchText: ''
    }
  },
  methods:{
    search(string){
      this.searchText = string
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
</style>
