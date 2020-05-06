<template>
    <div class="user_list">
        <!-- 按钮 -->
        <div class="btns">
            <el-button type="primary" size="small" @click="toAdd">新增用户</el-button>
        </div>
        <!-- 表格 -->
        <el-table :data="users" size = "small">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="用户名"></el-table-column>
            <el-table-column prop="phone" label="手机号"></el-table-column>
            <el-table-column prop="address" label="地址"></el-table-column>
            <el-table-column prop="gender" label="性别"></el-table-column>
            <el-table-column prop="birth" label="出生日期"></el-table-column>
            <el-table-column prop="postcode" label="邮政编码"></el-table-column>
            <el-table-column prop="register_time" label="注册日期"></el-table-column>
            <el-table-column prop="userface" label="头像"></el-table-column>
            <el-table-column label="操作" align="center" width="150" fixed="right">
                <template v-slot="slot">
                    <el-button size="mini" type = "text" @click="toBindRole(slot.row)">绑定角色</el-button>
                    <el-button size="mini" type = "text" @click="toDelete(slot.row.id)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!-- 模态框 -->
         
        <el-dialog title="绑定角色" :visible.sync="visible">
            {{user}}
            <el-form >
            <el-form-item label="用户名" label-width="80px">
                <strong>{{user.name}}</strong>
            </el-form-item>
            <el-form-item label="角色" label-width="80px">
               <el-checkbox-group v-model="user.roles">
                    <el-checkbox v-for="r in roles" :key=r.id :label="r.id" >{{r.name}}</el-checkbox>    
                </el-checkbox-group>
            </el-form-item>
           
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="visible = false">取 消</el-button>
                <el-button type="primary" @click="bindRoleHandler">确 定</el-button>
            </div>
        </el-dialog>
        <!-- 模态框 -->
        <!-- 新建用户模态框 -->
        <el-dialog :title="title" :visible.sync="user_visible">
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
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button size="small" @click="user_visible = false">取 消</el-button>
                    <el-button type="primary" size="small" @click="saveUserHandler">确 定</el-button>
                </div>
            </el-dialog>
         <!-- 新建用户模态框 -->
        
    </div>
</template>
<script>
import request from '@/utils/request'
import { get, post, del } from '@/utils/request'
import qs from 'querystring'
export default {
    data(){
        return{
            visible:false,
            form:{},
            users:[],//所有的用户信息
            user:{},//当前用户
            roles:[],//所有的角色信息
            user_visible:false,
            title: '添加用户',
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
        }
        }
    },
    created(){
        this.loadUsers();
        this.loadRoles();
    },
    methods:{
        //加载用户信息
        loadUsers(){
            request.get("/baseUser/findAllWithRole")
            .then(response=>{
                //数据清洗,将原先roles里面是角色对象替换成角色的id
                response.data.forEach(item=>{
                    item.roles=item.roles.map(r=>r.id)
                })
                this.users=response.data;
                
            })
            
        },
        //加载所有角色
        loadRoles(){
            request.get("/role/findAll")
            .then(response=>{
                this.roles = response.data;
            })
        },
        //绑定角色
        toBindRole(user){
            this.user = user;
            this.visible=true;   
        },
        //更新用户角色
        bindRoleHandler(){
            request.request({
                url:'/baseUser/setRoles',
                method:'post',
                headers:{
                    'Content-Type':'application/x-www-form-urlencoded'
                },
                data:qs.stringify(this.user)
            })
            .then(response=>{
                this.visible=false;
                this.$message({message:response.message,type:"success"})
                this.loadUsers();
            })
        },
        //添加用户
        toAdd(){
           this.form={};
           this.user_visible=true;     
        },
        //更新用户信息
        saveUserHandler(){
                this.$refs['user_form'].validate((valid) => {
                    if (valid) {
                    const url = '/baseUser/saveOrUpdate'
                    post(url, this.form)
                        .then(response => {
                        this.user_visible = false
                        this.$message({ message: response.message, type: 'success' })
                        this.loadUsers()
                        })
                    } else {
                    return false
                    }
                })
        },
         //删除用户信息
        toDelete(id){
            
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //交互
              let url = "/baseUser/deleteById"
              request.get(url,{params:{id:id}})
              .then(response=>{
                  //通知
                  this.$message({
                      message:response.message,
                      type:"success"
                  })
                  //重载数据
                  this.loadUsers();
              })
          
        })
        }
    }
}
</script>