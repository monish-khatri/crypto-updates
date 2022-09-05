<template>
 <nav class="navbar navbar-expand-lg navbar-dark bg-primary">
  <div class="container-fluid">
    <a class="navbar-brand" href="#">Crypto</a>
    <div class="collapse navbar-collapse" id="navbarColor01">
      <ul class="navbar-nav me-auto">
        <li class="nav-item">
          <a class="nav-link" href="#">Home</a>
        </li>
      </ul>
      <div class="d-flex">
        <input class="form-control me-sm-2" type="text" v-model="searchText" placeholder="Search">
        <button class="btn btn-secondary my-2 my-sm-0" type="button" @click="$emit('searchCoin',searchText)">Search</button>
        <button class="btn btn-secondary my-2 my-sm-0" type="button" @click="searchText = '',$emit('resetSearch')">Reset</button>
      </div>
    </div>
  </div>
</nav>
</template>

<script>
export default {
  name: 'NavBar',
  data(){
    return {
      searchText: ''
    }
  }
}
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
h3 {
  margin: 40px 0 0;
}
ul {
  list-style-type: none;
  padding: 0;
}
li {
  display: inline-block;
  margin: 0 10px;
}
a {
  color: #42b983;
}
</style>
