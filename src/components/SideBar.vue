<template>
    <div class="offcanvas offcanvas-end" tabindex="-1" id="offcanvasExample" aria-labelledby="offcanvasExampleLabel">
        <div class="offcanvas-header">
            <h5 class="offcanvas-title" id="offcanvasExampleLabel"></h5>
            <button type="button" class="btn-close text-reset" data-bs-dismiss="offcanvas" aria-label="Close"></button>
        </div>
        <div class="offcanvas-body">
            <div class="card mb-3">
            <h3 class="card-header" style="color:{{coin.color}}">{{coin.name}}</h3>
            <div class="card-body">
                <h5 class="card-title">{{coin.symbol}}</h5>
                <h6 class="card-subtitle text-muted"><a :href="coin.websiteUrl" target="_blank">{{coin.websiteUrl}}</a></h6>
            </div>
            <div class="card-body">
            <img :src="coin.iconUrl" :alt="coin.name" style="width:100%;height:50px;">
            <hr>
            <ul class="list-group list-group-flush">
                <li class="list-group-item">Price : {{coin.price}}</li>
                <li class="list-group-item">Market Capital: {{coin.marketCap}}</li>
                <li class="list-group-item">Change  ( 24 Hours ): <span v-if="coin.change > 0 " class='text-success'> +{{coin.change}}</span> <span v-else class='text-danger'> {{coin.change}}</span></li>
                <li class="list-group-item">BTC Price : {{coin.btcPrice}}</li>
                <li class="list-group-item">Volume  ( 24 Hours ): {{coin['24hVolume']}}</li>
            </ul>
            </div>
            <div class="card-body">
                <iframe :src="coin.coinrankingUrl"></iframe>
            </div>
            <div class="card-body">
                <h5 class="offcanvas-title" id="offcanvasExampleLabel">Description</h5>

                <h6 v-html="coin.description" class="card-subtitle text-muted"></h6>
            </div>
            <div class="card-footer text-muted">
               Developed by <h3>Monish Khatri</h3>
            </div>
            </div>
        </div>
    </div>
</template>

<script>
    export default {
        name: 'SideBar',
        props: {
            coin: Object,
        }
    }
</script>

<style lang="scss" scoped>

</style>