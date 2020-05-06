<template>
    <div class = "product_list">
        <!-- 按钮 -->
       <div class = "btns">
           <el-button type="primary" size="small" @click="toPulishProduct">发布农产品</el-button>
       </div>
       <!-- 表格 -->
     <el-table :data="product"  style="width: 100%">
      <el-table-column  prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="title" label="标题" width="180"></el-table-column>
      <el-table-column prop="price" label="价格"> </el-table-column>
      <!-- <el-table-column prop="media" label="url地址"> </el-table-column> -->
      <el-table-column  label="图片详情"> 
          <img :src="img" width="40" height="40"/>
      </el-table-column>
      <el-table-column prop="state" label="农产品属性"> </el-table-column>
      <el-table-column prop="category.name" label="所属栏目"> </el-table-column>
      <el-table-column prop="user.name" label="发布人"> </el-table-column>
      <el-table-column prop="description" label="描述信息"> </el-table-column>
       <el-table-column
      fixed="right"
      label="操作"
      align="center"
      width="150">
      <template slot-scope="scope">
        <!-- <el-button @click="toReview(scope.row)" type="text" size="small">查看</el-button> -->
        <el-button type="text" size="small" @click="toEdit(scope.row)">编辑</el-button>
        <el-button @click="toDelete(scope.row.id)" type="text" size="small">下架</el-button>
      </template>
    </el-table-column>
    </el-table>
       <!-- 分页 -->
    </div>
</template>
<script>
import request from '@/utils/request'
export default {
    data(){
        return{
            product:[],
            img:'http://q9svpbgj1.bkt.clouddn.com/image/farm_product%E7%8C%AA%E5%A4%B4%E8%82%89.png'
        }
   },

   created(){
      this.reloadData();
   },
   methods:{
       reloadData(){
                request.get('/product/findAllProduct')
        .then(result=>{
            this.product = result.data;
        })
       },
    //    toReview(){

    //    },
       toDelete(id){
           this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //交互
              let url = "/product/deleteById"
              request.get(url,{params:{id:id}})
              .then(response=>{
                  //通知
                  this.$message({
                      message:response.message,
                      type:"success"
                  })
                  //重载数据
                  this.reloadData();
              })
          
        })
       },
       toEdit(record){
          this.$router.push({path:'/Product/Editor',query:record})
       },
       toPulishProduct(){
           //跳转到编辑农产品界面
           this.$router.push({path:'/Product/Editor'})
       }
   }
}
</script>
<style scoped>

</style>