<template>
    <!-- 栏目列表 -->
    <div class="category_list">
        <el-dropdown v-for="c in categorys" >
            <el-button type="primary">
                {{c.name}}<i class="el-icon-arrow-down el-icon--right"></i>
            </el-button>
            <el-dropdown-menu slot="dropdown">
                <el-dropdown-item v-for="cc in c.children">{{cc.name}}</el-dropdown-item>
            </el-dropdown-menu>
        </el-dropdown>
    </div>
    <!-- 农产品列表 -->
    <div class="product_list">
        
    </div>
</template>
<script>
import request from '@/utils/request'
export default {
    data(){
        return{
            products:[],
            categorys:[],
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
       }
   }
}
</script>
<style scoped>

</style>