<template>
    <div class = "category_list">
        <!-- 按钮 -->
       <div class = "btns">
           <el-button type="primary" size="small" @click="toAdd">新增栏目</el-button>
           <el-button type="danger" size="small" @click="toBatchDelete">批量删除</el-button>
           {{ids}}
       </div>
       <!-- 表格 -->
     <el-table :data="category"  style="width: 100%" @selection-change="handleSelectionChange">
      <el-table-column type="selection" width="55"> </el-table-column>
      <el-table-column  prop="id" label="编号" width="180"></el-table-column>
      <el-table-column prop="name" label="栏目名" width="180"></el-table-column>
      <el-table-column prop="parentId" label="父栏目名" width="180"></el-table-column>
    <el-table-column
      fixed="right"
      label="操作"
      align="center"
      width="150">
      <template slot-scope="scope">
        <!-- <el-button @click="toReview(scope.row)" type="text" size="small">查看</el-button> -->
        <el-button type="text" size="small" @click="toEdit(scope.row)">编辑</el-button>
        <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除</el-button>
      </template>
    </el-table-column>
    </el-table>
       <!-- 模态框 -->
       <el-dialog title="新增模态框信息" :visible.sync="dialogFormVisible">
            <el-form :model="form">
                {{form}}
                <el-form-item label="栏目名称" label-width="80px">
                    <el-input v-model="form.name" autocomplete="off"></el-input>
                </el-form-item>
                
                <el-form-item label="父栏目" label-width="80px">
                    <el-select clearable  v-model="form.parentId" placeholder="请选择父栏目">
                        <el-option v-for="c in category" :key="c.id" :label="c.name" :value="c.id"></el-option>
                    </el-select>
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button size="small" @click="dialogFormVisible = false">取 消</el-button>
                <el-button size="small" type="primary" @click="submitHandler">确 定</el-button>
            </div>
        </el-dialog>
       <!-- 模态框 -->
    </div>
</template>
<script>
import request from '@/utils/request'
import qs from 'querystring'
// import qs from 'qs'
export default {
    data(){
        return{
            dialogFormVisible:false,
            form:{},
            category:[],
            ids:[]
        }
   },

   created(){
      this.reloadData();
   },
   methods:{
       handleSelectionChange(val){
           this.ids = val.map(item=>item.id);
           
       },
       submitHandler(){
           request.request({
               url:'http://localhost:8848/category/saveOrUpdate',
               method:"post",
               headers:{
                   'Content-Type':'application/x-www-form-urlencoded'
               },
               data:qs.stringify(this.form)
           })
           .then(response=>{
               //提示信息
                this.$message({
                    message:response.message,
                    type:"success"
                })
                //关闭模态框
                this.dialogFormVisible = false;
                //重载数据
                this.reloadData();
           })
       },
       reloadData(){
                request.get('http://localhost:8848/category/findAll')
        .then(result=>{
            this.category = result.data;
        })
       },
    //    toReview(){

    //    },
    //批量删除
    toBatchDelete(){
        // let url = "http://localhost:8848/category/batchDelete"
        this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
        //交互
              let url = "/category/batchDelete"
              request.post(url,this.ids)
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
       toDelete(id){
           this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //交互
              let url = "/category/deleteById"
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
          this.dialogFormVisible = true;
          //为form绑定要修改的值
          this.form = record;
       },
       toAdd(){
           //跳转到编辑农产品界面
           this.form = {};
           this.dialogFormVisible = true;
       }
   }
}
</script>
<style scoped>

</style>