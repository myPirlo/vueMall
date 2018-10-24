<template>
    <div>
            <nav-header></nav-header>
            <nav-bread>
                <span>goodList</span>
            </nav-bread>

            
            
            <div class="accessory-result-page accessory-page">
            <div class="container">
                <div class="filter-nav">
                <span class="sortby">Sort by:</span>
                <a href="javascript:void(0)" class="default cur">Default</a>
                <a @click="sort" href="javascript:void(0)"  class="price">Price <svg class="icon icon-arrow-short"><use xlink:href="#icon-arrow-short"></use></svg></a>
                <a href="javascript:void(0)" class="filterby stopPop" @click="showFilter">Filter by</a>
                </div>
                <div class="accessory-result">
                <!-- filter -->
                <div class="filter stopPop" id="filter" v-bind:class="{'filterby-show':filterBy}">
                    <dl class="filter-price">
                        <dt>Price:</dt>
                        <dd><a href="javascript:void(0)" v-bind:class="{'cur':priceChecked=='all'}" @click="filterAll">All</a></dd>
                        <dd v-for="(price,index) in priceFilter" :key="index">
                            <a href="javascript:void(0)" @click="filter(index)" v-bind:class="{'cur':priceChecked==index}" >{{price.startPrice}} - {{price.endPrice}}</a>
                        </dd>
                    </dl>
                </div>

                <!-- search result accessories list -->
                <div class="accessory-list-wrap">
                    <div class="accessory-list col-4">
                    <ul>
                        <li v-for="(item,index) in goodLists" :key="index">
                            <div class="pic">
                                <a href="#"><img v-bind:src="'/static/'+item.productImage" alt=""></a>
                            </div>
                            <div class="main">
                                <div class="name">{{item.productName}}</div>
                                <div class="price">价格 : {{item.salePrice}}</div>
                                <div class="btn-area">
                                <a href="javascript:;"  @click="addtoCart(item.productId)" class="btn btn--m">加入购物车</a>
                                </div>
                            </div>
                        </li>
                    </ul>
                    </div>
                </div>
                </div>
            </div>
            </div>
            <div class="md-overlay" v-show="overMask" @click="closeMask"> </div>
            <modal v-bind:mdShow="mdShow" v-on:close="closeModal">
                <p slot="message">
                    请先登录,否则无法加入购物车
                </p>
                <div slot="btnGroup">  
                    <a class="btn btn--m"  href="javascript:;" @click="mdShow=false">关闭</a>
                </div>
            </modal>
            <modal v-bind:mdShow="mdShowCart" v-on:close="closeModal">
                <p slot="message">
                    加入购物车成功
                </p>
                <div slot="btnGroup">  
                    <a class="btn btn--m"  href="javascript:;" @click="mdShowCart=false">继续购物</a>
                    <router-link class="btn btn--m btn--red" href="javascript:;" to="/cart">查看购物车</router-link>
                </div>
            </modal>
            <nav-footer></nav-footer>


    </div>
</template>

<script>
import '../assets/css/base.css'
import '../assets/css/product.css'
import NavHeader from '@/components/header' 
import NavFooter from '@/components/footer' 
import NavBread  from '@/components/bread' 
import Modal  from '@/components/Modal' 
import axios from 'axios'


export default {
    data() {
        return {
            goodLists:[],
            priceFilter:[
                {
                    "startPrice":0,
                    "endPrice":500
                },{
                    "startPrice":500,
                    "endPrice":1000
                },{
                    "startPrice":1000,
                    "endPrice":5000
                }
            ],
            priceChecked:"all",
            filterBy:false,
            overMask:false,
            page:1,
            pageSize:80,
            sotrType:true,
            mdShow:false,
            mdShowCart:false
        }
    },
    mounted(){
        this.getGoosLists();
    },
    
    components:{
        NavHeader,
        NavFooter,
        NavBread,
        Modal
    },
    methods: {
        getGoosLists() {
            var thisParams={
                page:this.page,
                pageSize:this.pageSize,
                sort:this.sotrType?1:-1,
                priceLevel:this.priceChecked
            }
            axios.get("/goods/list",{
                params:thisParams
            }).then((result)=>{
                
                this.goodLists=result.data.result.list
                console.log(this.goodLists)
            })
        },
        sort(){
            this.sotrType=!this.sotrType;
            this.page=1;
            this.getGoosLists();

        },
        showFilter(){
            this.filterBy=true;
            this.overMask=true;
        },
        closeMask(){
            this.filterBy=false;
            this.overMask=false;
        },
        filter(index){
            this.priceChecked=index
            this.closeMask()
            this.page=1;
            this.getGoosLists();
        },
        filterAll(){
            this.priceChecked="all";
            this.page=1;
            this.getGoosLists();
        },
        closeModal(){
            this.mdShow=false;
            this.mdShowCart=false;
        },
        addtoCart(productId){
            axios.post("/goods/addCart",{
                productId:productId
            }).then((result)=>{
                
                var res=result.data;
                if(res.status!=0){
                   
                    this.mdShow=true;
                    
                }else{
                    this.mdShowCart=true;
                    this.$store.commit("updateCartCount",1);
                }
                
            })
        }

    },
};
</script>

<style scoped>
</style>