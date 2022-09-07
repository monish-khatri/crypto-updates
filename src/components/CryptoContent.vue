<template>
<h1>Apne Crypto Liya kya?</h1>

<table class="table table-hover">
  <thead>
    <tr>
      <th scope="col" width="20%">Coin</th>
      <th scope="col" width="30%">Price</th>
      <th scope="col" width="30%">Change ( 24 Hours )</th>
      <th scope="col" width="20%">Action</th>
    </tr>
  </thead>
  <tbody>
     <template v-if="coinDetails.length">
        <template v-for="(coin,index) in coinDetails" :key="index">
            <tr :class="(index % 2 == 0) ? 'table-light' :'table-light'">
              <td>
                <img :src="coin.iconUrl" :alt="coin.name" style="width:50px;height:50px;"><span class="p-2">{{coin.symbol}}</span>
              </td>
              <td>{{formatPrice(coin.price)}}</td>
              <td><span :class="coin.change > 0 ?'text-success': 'text-danger'">{{coin.change}}</span></td>
              <td>
                <button @click="toggleSideBar(coin)" class="btn btn-primary" type="button" data-bs-toggle="offcanvas" data-bs-target="#offcanvasExample" aria-controls="offcanvasExample">
                  More Detail
                </button>
              </td>
            </tr>
        </template>
      </template>
      <template v-else>
          <tr><td colspan="5">No Record Yet</td></tr>
      </template>
  </tbody>
</table>
<div v-if="pagination.totalPage > 0" class="container">
  <ul class="pagination">
    <li :class="currentPage == 1 ?'page-item disabled':'page-item'">
      <a class="page-link" @click="setPaginationData(currentPage-1)">&laquo;</a>
    </li>
    <template v-for="page in pagination.totalPage" :key="page">
      <li :class="currentPage == page ?'page-item active':'page-item'">
        <a class="page-link" @click="setPaginationData(page)" >{{page}}</a>
      </li>
    </template>
    <li  :class="currentPage == pagination.totalPage ?'page-item disabled':'page-item'">
      <a class="page-link" @click="setPaginationData(currentPage+1)">&raquo;</a>
    </li>
  </ul>
</div>
<SideBar v-show="showSideBar" :coin="sideBarData" />
</template>

<script>
import axios from "axios";
import SideBar from "./SideBar.vue";
export default {
  name: 'CryptoContent',
  components:{
    SideBar,
  },
  created(){
    this.getCoinUpdate()
  },
  data(){
    return{
      coinDetails: {},
      showSideBar: false,
      sideBarData: {},
      pagination:{
        totalPage: 0,
        limit: 50,
        offset: 0,
      },
      currentPage: 1,
    }
  },
  methods: {
    getCoinUpdate(){
      const options = {
          method: 'GET',
          url: 'https://coinranking1.p.rapidapi.com/coins',
          params: {
            referenceCurrencyUuid: 'yhjMzLPhuIDl',
            timePeriod: '24h',
            'tiers[0]': '1',
            orderBy: 'price',
            orderDirection: 'desc',
            limit: this.pagination.limit,
            offset: this.pagination.offset
          },
          headers: {
            'X-RapidAPI-Key': '7cf2e14394mshf50f6cdaf1bf272p128751jsncd8219200b82',
            'X-RapidAPI-Host': 'coinranking1.p.rapidapi.com'
          }
      };

      axios.request(options).then((response) => {
        this.coinDetails = response.data.data.coins;
        this.pagination.totalPage = parseInt(parseInt(response.data.data.stats.total)/this.pagination.limit)
      }).catch(function (error) {
        console.error(error);
      });
    },
    formatPrice(price){
      return parseFloat(price).toFixed(2)
    },
    toggleSideBar(coin){
      this.getCoinDetails(coin.uuid)
    },
    getCoinDetails(uuid){
      const options = {
        method: 'GET',
        url: 'https://coinranking1.p.rapidapi.com/coin/'+uuid,
        params: {referenceCurrencyUuid: 'yhjMzLPhuIDl', timePeriod: '24h'},
        headers: {
          'X-RapidAPI-Key': '7cf2e14394mshf50f6cdaf1bf272p128751jsncd8219200b82',
          'X-RapidAPI-Host': 'coinranking1.p.rapidapi.com'
        }
      };

      axios.request(options).then((response) => {
        this.sideBarData = response.data.data.coin
        this.showSideBar = true
      }).catch(function (error) {
        console.error(error);
      });
    },
    setPaginationData(page){
      if (page > 1) {
        this.currentPage = page
        this.pagination.offset = page * 50;
      } else {
        this.currentPage = 1
        this.pagination.offset = 0;
      }
      this.getCoinUpdate()
    },
    search(string) {
      const options = {
        method: 'GET',
        url: 'https://coinranking1.p.rapidapi.com/search-suggestions',
        params: {referenceCurrencyUuid: 'yhjMzLPhuIDl', query: string},
        headers: {
          'X-RapidAPI-Key': '7cf2e14394mshf50f6cdaf1bf272p128751jsncd8219200b82',
          'X-RapidAPI-Host': 'coinranking1.p.rapidapi.com'
        }
      };

      axios.request(options).then((response) => {
        this.coinDetails = response.data.data.coins;
        if(this.coinDetails.length < 50 ){
          this.pagination.totalPage = {}
        } else {
          this.pagination.totalPage = parseInt(parseInt(this.coinDetails.length)/this.pagination.limit)
        }
      }).catch(function (error) {
        console.error(error);
      });
    }
  },
  watch:{
    searchText(newValue){
      if(newValue != ''){
        this.search(newValue);
      }else{
        this.getCoinUpdate()
      }
    }
  },
  props:{
    searchText: String
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
