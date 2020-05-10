<template>
    <div>
    <!-- 首栏(栏目和搜索框) -->
    <div class="header">
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
        <!-- 搜索框 -->
        <div class="search" >
            <el-select 
                v-model="value" 
                multiple  
                filterable="true"  
                remote="true"  
                reserve-keyword 
                placeholder="请输入关键词" 
                
                :remote-method="searchFor"
                :loading="loading"
                
                >
                <el-option
                v-for="item in options"
                :key="item.id"
                :label="item.title"
                :value="item">
                </el-option>
            </el-select>
        </div>
    </div>
        <br/><br/>
        <!-- 农产品列表 -->
        <div class="product_list"> 
            <!-- <div class="product" v-for="product in products" :key="product.id" v-if="product.state=='采购'"> -->
                 
                  <!-- 表格 -->
                <el-table :data="products"  style="width: 100%" >
                <el-table-column  prop="id" label="编号" width="180"></el-table-column>
                <el-table-column  prop="title" label="标题" width="180"></el-table-column>
                <el-table-column  prop="category.name" label="所属栏目"> </el-table-column>
                <el-table-column  prop="user.name" label="发布人"> </el-table-column>
                <el-table-column  prop="description" label="描述信息"> </el-table-column>
                <el-table-column  prop="user.phone" label="联系方式"> </el-table-column>
                <el-table-column  prop="user.address" label="地址"> </el-table-column>
                <!-- <el-table-column
                fixed="right"
                label="操作"
                align="center"
                width="150">
                <template slot-scope="scope">
                    
                    <el-button type="text" size="small" @click="toEdit(scope.row)">编辑</el-button>
                    <el-button @click="toDelete(scope.row.id)" type="text" size="small">下架</el-button>
                </template>
                </el-table-column> -->
                </el-table>
            <!-- </div> -->
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
            options:[],
        }
   },

   created(){
      this.reloadProduct();
      this.reloadCategory();
   },
   methods:{
       reloadProduct(){
                request.get('/product/findAllSupply')
        .then(result=>{
            this.products = result.data;
            this.options = result.data;
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
       },
       searchFor(value){
            request.get("/product/findByNameDemand?name="+value)
          .then(response=>{
            this.products = response.data;
            })
       }
   }
}
</script>
<style scoped>
    .product{
         float:left;
    }
    .category_list{
        float:left;
        
    }
    .search{
        float:right;
        
    }
</style>