<template>
    <div class="myname">
        {{this.name}}<br/>
        {{form}}
        <div class="me">
                <el-form ref="user_form" :model="form" :rules="rules">
                <el-form-item label="用户名" label-width="80px" prop="name">
                <el-input v-model="form.name" autocomplete="off" />
                </el-form-item>
                <el-form-item label="密码" label-width="80px" prop="password">
                <el-input v-model="form.password" autocomplete="off" />
                </el-form-item>
                <el-form-item label="性别" label-width="80px" prop="gender">
                <el-radio-group v-model="form.gender">
                    <el-radio label="男">男</el-radio>
                    <el-radio label="女">女</el-radio>
                </el-radio-group>
                </el-form-item>
                <el-form-item label="手机号" label-width="80px">
                    <el-input v-model="form.phone" autocomplete="off" />
                </el-form-item>
                <el-form-item label="邮政编码" label-width="80px">
                    <el-input v-model="form.postcode" autocomplete="off" />
                </el-form-item>
                <el-form-item label="出生日期" label-width="80px">
                <el-date-picker v-model="form.birth" value-format="timestamp" type="date" placeholder="选择日期" />
                </el-form-item>
 
                <el-form-item>
                    <el-button type="primary" @click="saveOrUpdate">确认</el-button>
                    <el-button type="primary" @click="back">返回</el-button>
                </el-form-item>
            </el-form>
        </div>
        <br/><br/>
        <!-- 我的订单----表格形式展示 -->{{this.order}}
        <div class = "order_list">
        <!-- 表格 -->
            <el-table :data="order"  style="width: 100%">
                <el-table-column prop="id" label="编号" width="180"></el-table-column>
                <!-- <el-table-column prop="state" label="订单状态" width="180"></el-table-column> -->
                <el-table-column prop="date" label="下单时间"> </el-table-column>
                <el-table-column prop="product.title" label="产品"> </el-table-column>
                <!-- <el-table-column prop="user.name" label="下单人"> </el-table-column> -->
                <el-table-column prop="name" label="收件人"> </el-table-column>
                <el-table-column prop="address" label="收件地址"> </el-table-column>
                <el-table-column prop="phone" label="联系电话"> </el-table-column>
                <el-table-column
                    fixed="right"
                    label="操作"
                    align="center"
                    width="150">
                    <template slot-scope="scope">
                        <el-button @click="toEvaluate(scope.row)" type="text" size="small">评论</el-button>
                        <el-button @click="toDelete(scope.row.id)" type="text" size="small">删除订单</el-button>
                    </template>
                </el-table-column>
            </el-table>
        </div>

        <!-- 添加评论模态框 -->
        <el-dialog :title="title" :visible.sync="evaluate_visible">
         {{form2}}
            <el-form ref="envaluate_form" :model="form2" :rules="rules2">
                <el-form-item label="评论内容" label-width="80px" prop="content">
                <el-input type="textarea" v-model="form2.content" autocomplete="off" />
                </el-form-item>
                <el-form-item label="图片" label-width="80px" >
                    <el-upload
                        class="upload-demo"
                        ref="upload"
                        action="https://jsonplaceholder.typicode.com/posts/"
                        :on-preview="handlePreview"
                        :on-remove="handleRemove"
                        :file-list="fileList"
                        :auto-upload="false">
                        <el-button slot="trigger" size="small" type="primary">选取文件</el-button>
                        <el-button style="margin-left: 10px;" size="small" type="success" @click="submitUpload">上传到服务器</el-button>
                        <div slot="tip" class="el-upload__tip">只能上传jpg/png文件，且不超过500kb</div>
                    </el-upload>
                </el-form-item>
                <el-form-item label="用户id" label-width="80px" hidden="true">
                    <el-input  v-model="form2.userId" autocomplete="off" />
                </el-form-item>
                <el-form-item label="用户id" label-width="80px" hidden="true">
                    <el-input  v-model="form2.productId" autocomplete="off" />
                </el-form-item>
                
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button size="small" @click="evaluate_visible = false">取 消</el-button>
                    <el-button type="primary" size="small" @click="toSave">确 定</el-button>
                </div>
            </el-dialog> 
    </div>

    
</template>
<script>
import { mapGetters } from 'vuex'
import request from '@/utils/request'
import qs from 'qs'
import { get, del, post } from '@/utils/request'

export default {
    data(){
        return{
            form:{},
            rules: {
                name: [
                { required: true, message: '请输入用户名', trigger: 'change' }
                ],
                password: [
                { required: true, message: '请输入密码', trigger: 'change' }
                ],
                gender: [
                { required: true, message: '请选择性别', trigger: 'change' }
                ]
            },
            rules2:{
                content: [
                { required: true, message: '评论信息', trigger: 'change' }
                ]    
            },
            order:[],
            evaluate_visible:false,
            title:'评论',
            form2:{},
        }
    },
    computed: {
    ...mapGetters([
      'name',
    ])
  },
  created(){
      this.loadUser(this.name);
      this.loadOrder(this.name);
  },
  methods:{
      toSave(){
          this.$refs['envaluate_form'].validate((valid) => {
                    if (valid) {
                    const url = '/Order/toEvaluate'
                    post(url, this.form2)
                        .then(response => {
                        this.evaluate_visible = false
                        this.$message({ message: response.message, type: 'success' })
                        })
                    } else {
                    return false
                    }
                })
      },
      loadOrder(userName){
          request.get('/Order/findByUserName?name='+userName)
            .then(response=>{
                this.order = response.data;
            })
      },
      toEvaluate(order){
          this.form2 = order;
          this.evaluate_visible=true;
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
                    this.loadOrder(this.name);
                })
            
            })
          
      },
      loadUser(name){
          request.get('/baseUser/findByName?name='+name)
            .then(response=>{
                this.form = response.data;
            })
      },
      saveOrUpdate(){
          request(
                {
                    method:"post",
                    url:'/baseUser/saveOrUpdate',
                    data:qs.stringify(this.form),
                    headers:{
                        'Content-Type':'application/x-www-form-urlencoded'
                    }
                }
            ).then(result=>{
        this.$message({
            message:result.message,
            type:"success"
        });
        this.back();
      })
      },
      back(){
            this.$router.go(-1);
        },
  }
}
</script>