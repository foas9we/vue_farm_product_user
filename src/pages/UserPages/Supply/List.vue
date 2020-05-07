<template>
    <div>
    <!-- 栏目列表 -->
        <div class="category_list">
            <el-dropdown v-for="c in categorys" @command="toFiltrate">
                <el-button type="text">
                    {{c.name}}<i class="el-icon-arrow-down el-icon--right"></i>
                </el-button>
                <el-dropdown-menu slot="dropdown">
                    <el-dropdown-item v-for="cc in c.children" :command=cc.id>{{cc.name}}</el-dropdown-item>
                    
                </el-dropdown-menu>
            </el-dropdown>
           
        </div>
        <!-- 农产品列表 -->
        <div class="product_list"> 
            <div class="product" v-for="product in products" :key="product.id" v-if="product.state=='供货'">
                 
                <router-link :to="'/product/' + product.id"
                            class="product-main">
                    <!-- 依次显示商品图片、标题、单价 -->
                    <img :src="product.media">
                    <h4>{{product.title}}</h4>
                    <!-- <h4>销量{{info.sales}}</h4> -->
                    <div class="product-cost">￥ {{product.price}}</div>
                </router-link>
                <div class="product-not-found"
                    v-show="!products.length">暂无相关商品</div>
                </div>
            
        </div>       
    </div>
    
</template>
<script>
import request from '@/utils/request'
export default {
    data(){
        return{
            products:[],
            categorys:[],
            props:{multiple: true ,label:'name',value:'id',emitPath:false},
        }
   },

   created(){
      this.reloadProduct();
      this.reloadCategory();
   },
   methods:{
       reloadProduct(){
                request.get('/product/findAllProduct')
        .then(result=>{
            this.products = result.data;
        })
       },
       reloadCategory(){
                request.get('/category/cascodeFindAll')
            .then(result=>{
                this.categorys = result.data;
            })
       },
       toFiltrate(command){
          request.get("/product/findByCategory?id="+command)
          .then(response=>{
            this.products = response.data;
        })
       }
   }
}
</script>
<style scoped>
    .product{
            width: 25%;
            float: left;
        }
    .product-main{
        display: block;
        margin: 16px;
        padding: 16px;
        border: 1px solid #dddee1;
        border-radius: 6px;
        overflow: hidden;
        background: #fff;
        text-align: center;
        position: relative;
    }
    .product-main img{
        width: 100%;
        height: 350px;
    }
    h4{
        color: #222;
        overflow: hidden;
        text-overflow: ellipsis;
        white-space: nowrap;
    }
    .product-main:hover h4{
        color: #0070c9;
    }
    .product-color{
        display: block;
        width: 16px;
        height: 16px;
        border: 1px solid #dddee1;
        border-radius: 50%;
        margin: 6px auto;
    }
    .product-cost{
        color: #de4037;
        margin-top: 6px;
    }
    .product-add-cart{
        display: none;
        padding: 4px 8px;
        background: #2d8cf0;
        color: #fff;
        font-size: 12px;
        border-radius: 3px;
        cursor: pointer;
        position: absolute;
        top: 5px;
        right: 5px;
    }
    .product-main:hover .product-add-cart{
        display: inline-block;
    }
</style>