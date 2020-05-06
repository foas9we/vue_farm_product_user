<template>
    <div class = "evaluate_list">
       <!-- 表格 -->
     <el-table :data="evaluate"  style="width: 100%">
      <el-table-column  prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="content" label="评论" width="180"></el-table-column>
      <el-table-column prop="date" label="评论时间"> </el-table-column>
      <el-table-column prop="picture" label="图片"> </el-table-column>
      <el-table-column prop="user.name" label="评论人"> </el-table-column>
      <el-table-column prop="productId" label="产品编号"> </el-table-column>
       <el-table-column
      fixed="right"
      label="操作"
      align="center"
      width="150">
      <template slot-scope="scope">
        <el-button @click="toDelete(scope.row.id)" type="text" size="small">撤回</el-button>
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
            evaluate:[]
        }
   },

   created(){
      this.reloadData();
   },
   methods:{
       reloadData(){
                request.get('/evaluate/cascadeFindAll')
        .then(result=>{
            this.evaluate = result.data;
        })
       },
       toDelete(id){
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //交互
              let url = "/evaluate/deleteById"
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