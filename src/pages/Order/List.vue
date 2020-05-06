<template>
    <div class = "order_list">
       <!-- 表格 -->
     <el-table :data="order"  style="width: 100%">
      <el-table-column  prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="state" label="订单状态" width="180"></el-table-column>
      <el-table-column prop="date" label="下单时间"> </el-table-column>
      <el-table-column prop="product.title" label="产品"> </el-table-column>
      <el-table-column prop="user.name" label="用户名"> </el-table-column>
       <el-table-column
      fixed="right"
      label="操作"
      align="center"
      width="150">
      <template slot-scope="scope">
        <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除订单</el-button>
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
            order:[]
        }
   },

   created(){
      this.reloadData();
   },
   methods:{
       reloadData(){
                request.get('/Order/cascadeFindAll')
        .then(result=>{
            this.order = result.data;
        })
       },
       toDelete(id){
           this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //交互
              let url = "/Order/deleteById"
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
       }
       
   }
}
</script>
<style scoped>

</style>