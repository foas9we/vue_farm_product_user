<template>
    <div class="role_list">
        <div class="btns">
            <el-button type="primary" size="small" @click="toAdd">新增角色</el-button>
        </div>
        <el-table :data="roles" size = "small">
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="角色名"></el-table-column>
            
            <el-table-column label="操作" aling="center" width="100">
                <template v-slot="slot">
                    <el-button size="mini" type = "text" @click="toBindPrivilege(slot.row)">授权</el-button>
                    <el-button size="mini" type = "text" @click="toDelete(slot  .row.id)">删除</el-button>
                </template>
            </el-table-column>
        </el-table>
        <!-- 模态框 -->
         
        <el-dialog title="授权" :visible.sync="visible">
            {{role.privilege}}
            <el-form >
            <el-form-item label="角色名" label-width="80px">
                <strong>{{role.name}}</strong>
            </el-form-item>
            <el-form-item label="权限" label-width="80px">
               <el-cascader-panel 
                    v-model="role.privilege"
                    :props="props"
                    :options="privileges" 
                    clearable />
            </el-form-item>
           
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button @click="visible = false">取 消</el-button>
                <el-button type="primary" @click="toBindPrivilegeHandler" >确 定</el-button>
            </div>
        </el-dialog>
        <!-- 模态框 -->
        <!-- 新建角色模态框 -->
        <el-dialog :title="title" :visible.sync="role_visible">
            <el-form ref="role_form" :model="form" :rules="rules">
                <el-form-item label="角色名称" label-width="80px" prop="name">
                <el-input v-model="form.name" autocomplete="off" />
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button size="small" @click="role_visible = false">取 消</el-button>
                <el-button type="primary" size="small" @click="saveRoleHandler">确 定</el-button>
            </div>
        </el-dialog>
        <!-- 新建角色模态框 -->
    </div>
</template>
<script>
import request from '@/utils/request'
import qs from 'querystring'
import { get, post, del } from '@/utils/request'
export default {
    data(){
        return{
            roles:[],
            role:{},
            visible:false,
            privileges:[],
            props:{multiple: true ,label:'name',value:'id',emitPath:false},
            ids:[],
            role_visible:false,
            title:'添加角色',
            form:{},
            rules: {
                name: [
                { required: true, message: '请输入角色名称', trigger: 'change' }
                ]
            }
            
        }
    },
    created(){
        this.loadRoles();
        this.loadPrivileges();
    },
    methods:{
        //加载角色信息
        loadRoles(){
            request.get("/role/findAllRoleWithPrivilege")
            .then(response=>{
                //将权限转化为id数组
                response.data.forEach(item=>{
                    item.privilege=item.privilege.map(p=>{
                        return p.id
                    })
                })
                this.roles = response.data;
            })

            

        },
        //绑定角色
        toBindPrivilege(role){
            this.role = role;
            this.visible=true;
             
        },
        //加载权限信息
        loadPrivileges(){
            request.get("/privilege/findAllWithChildren")
            .then(response=>{
                this.privileges = response.data;
            })
        },
        //权限绑定控制器，按下确定时触发
        toBindPrivilegeHandler(){
            request.request({
                url:'/role/setPrivilegeToRole',
                method:'post',
                headers:{
                    'Content-Type':'application/x-www-form-urlencoded'
                },
                data:qs.stringify(this.role)
            })
            .then(response=>{
                this.visible=false;
                this.$message({message:response.message,type:"success"})
                this.loadRoles();
            })
        },
        toAdd() {
            this.form = {}
            this.role_visible = true
        },
        saveRoleHandler(){
            this.$refs['role_form'].validate((valid) => {
                if (valid) {
                request.request({
                    url: '/role/saveOrUpdate',
                    method: 'post',
                    headers: {
                    'Content-Type': 'application/x-www-form-urlencoded'
                    },
                    data: qs.stringify(this.form)
                })
                    .then(response => {
                    this.role_visible = false
                    this.$message({ message: response.message, type: 'success' })
                    this.loadRoles()
                    })
                } else {
                return false
                }
            })
        },
        //删除角色信息
        toDelete(id){
            
          this.$confirm('此操作将永久删除该文件, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning'
        }).then(() => {
          //交互
              let url = "/role/deleteById"
              request.get(url,{params:{id:id}})
              .then(response=>{
                  //通知
                  this.$message({
                      message:response.message,
                      type:"success"
                  })
                  //重载数据
                  this.loadRoles();
              })
          
        })
        }
    }
}
</script>