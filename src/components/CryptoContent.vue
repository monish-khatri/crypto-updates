<template>
  <div class="col-md-12 row">
    <div class="col-md-10">
        <h1>Apne Crypto Liya kya?</h1>
    </div>

    <div class="col-md-2 row">
      <div class="col-md-4">
          <h6 class="mt-3 text-danger">Currency:</h6>
      </div>
      <div class="col-md-8">
        <select class="form-select mt-2 col-md-8" @change="changeCurrency($event)" id="currencySelect">
          <template v-if="currencyList.length">
            <template v-for="(currency,index) in currencyList" :key="index">
              <option :symbol="currency.sign" :value="currency.uuid" :selected="(currencyDetails.id == currency.uuid)? true: false">{{currency.name}}</option>
            </template>
          </template>
        </select>
      </div>
    </div>
  </div>

<table class="table table-hover">
  <thead>
    <tr>
      <th scope="col" width="5%">Sr. No</th>
      <th scope="col" width="10%">Symbol</th>
      <th scope="col" width="10%">Coin</th>
      <th scope="col" width="30%">Price</th>
      <th scope="col" width="15%">Action</th>
    </tr>
  </thead>
  <tbody>
     <template v-if="coinDetails.length">
        <template v-for="(coin,index) in coinDetails" :key="index">
            <tr :class="(index % 2 == 0) ? 'table-light' :'table-light'">
              <td>
                <span class="p-2">{{index+1}}</span>
              </td>
              <td>
                <img :src="coin.iconUrl" :alt="coin.name" style="width:50px;height:50px;">
              </td>
              <td>
                <span class="p-2">{{coin.symbol}}</span>
              </td>
              <td>{{currencyDetails.symbol}} {{formatPrice(coin.price)}}</td>
              <td>
                <button @click="toggleSideBar(coin)" class="btn btn-primary" type="button" data-bs-toggle="offcanvas" data-bs-target="#offcanvasExample" aria-controls="offcanvasExample">
                  More Detail
                </button>
              </td>
            </tr>
        </template>
      </template>
      <template v-else>
          <tr><td colspan="6">No Record Found</td></tr>
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
<SideBar v-show="showSideBar" :coin="sideBarData" :currencyDetails="currencyDetails"/>
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
    this.getCurrencies()
    this.getCoinUpdate()
  },
  data(){
    return{
      apiKey:'7cf2e14394mshf50f6cdaf1bf272p128751jsncd8219200b82',
      apiHost:'coinranking1.p.rapidapi.com',
      coinDetails: {},
      showSideBar: false,
      sideBarData: {},
      pagination:{
        totalPage: 0,
        limit: 50,
        offset: 0,
      },
      currentPage: 1,
      currencyDetails:{
        id:'6mUvpzCc2lFo',
        symbol: '₹'
      },
      currencyList: {},
    }
  },
  methods: {
    getCoinUpdate(){
      const options = {
          method: 'GET',
          url: 'https://coinranking1.p.rapidapi.com/coins',
          params: {
            referenceCurrencyUuid: this.currencyDetails.id,
            timePeriod: '24h',
            'tiers[0]': '1',
            orderBy: 'price',
            orderDirection: 'desc',
            limit: this.pagination.limit,
            offset: this.pagination.offset
          },
          headers: {
            'X-RapidAPI-Key': this.apiKey,
            'X-RapidAPI-Host': this.apiHost
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
        params: {referenceCurrencyUuid: this.currencyDetails.id, timePeriod: '24h'},
        headers: {
          'X-RapidAPI-Key': this.apiKey,
          'X-RapidAPI-Host': this.apiHost
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
        params: {referenceCurrencyUuid: this.currencyDetails.id, query: string},
        headers: {
          'X-RapidAPI-Key': this.apiKey,
          'X-RapidAPI-Host': this.apiHost
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
    },
    getCurrencies(){
      const options = {
        method: 'GET',
        url: 'https://coinranking1.p.rapidapi.com/reference-currencies',
        params: {limit: '100', offset: '0'},
        headers: {
          'X-RapidAPI-Key': this.apiKey,
          'X-RapidAPI-Host': this.apiHost
        }
      };
      axios.request(options).then((response) => {
        this.currencyList = response.data.data.currencies;
      }).catch(function (error) {
        console.error(error);
      });
    },
    changeCurrency(event){
      this.currencyDetails.id = event.target.value;
      this.getCoinUpdate()
      var options = event.target.options
      if (options.selectedIndex > -1) {
        this.currencyDetails.symbol = options[options.selectedIndex].getAttribute('symbol');
      }
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
.offcanvas.offcanvas-end {
  width:800px !important
}
</style>
