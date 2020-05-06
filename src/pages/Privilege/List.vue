<template>
    <div class="privilege_list">
        <div class="btns">
            <el-button type="primary" size="small">新增权限</el-button>
        </div>
        <!-- 表格 -->
        <el-table :data="privileges" size = "small" row-key="id" >
            <el-table-column prop="id" label="编号"></el-table-column>
            <el-table-column prop="name" label="权限名"></el-table-column>
            <el-table-column prop="type" label="类型"></el-table-column>
            <el-table-column prop="route" label="路径"></el-table-column>
            
            <el-table-column label="操作" aling="center" width="100">
                <template v-slot="slot">
                    <el-button size="mini" type = "text" @click="toEdit(slot.row)">修改</el-button>
                </template>
            </el-table-column>
        </el-table>

        <!-- 模态框 -->
        <el-dialog :title="title" :visible.sync="visible">
            <!-- {{form}} -->
            <el-form ref="privilege_form" :model="form" :rules="rules">
                <el-form-item label="名称" label-width="80px" prop="name">
                <el-input v-model="form.name" autocomplete="off" />
                </el-form-item>
                <el-form-item label="路径" label-width="80px" prop="route">
                <el-input v-model="form.route" autocomplete="off" />
                </el-form-item>
                <el-form-item label="类型" label-width="80px" prop="type">
                <el-select v-model="form.type" clearable placeholder="请选择">
                    <el-option label="菜单" value="menu" />
                    <el-option label="方法" value="method" />
                </el-select>
                <el-popover
                    placement="top-start"
                    title="提示"
                    width="300"
                    trigger="hover"
                    content="菜单控制着用户登录后可以动态显示的菜单树；方法为具体的权限，控制着是否可以调用某些接口"
                >
                    <el-button slot="reference" type="text"><i class="el-icon-location-information" /></el-button>
                </el-popover>
                </el-form-item>
                <el-form-item label="父权限" label-width="80px">
                <el-cascader
                    v-model="form.parentId"
                    clearable
                    :options="paretnPrivileges"
                    :props="{ expandTrigger: 'hover', value: 'id', label: 'name' ,checkStrictly:true}"
                />
                </el-form-item>
                <el-form-item label="描述" label-width="80px">
                <el-input v-model="form.description" type="textarea" autocomplete="off" />
                </el-form-item>
            </el-form>
            <div slot="footer" class="dialog-footer">
                <el-button size="small" @click="visible = false">取 消</el-button>
                <el-button type="primary" size="small" @click="submitHandler">确 定</el-button>
            </div>
        </el-dialog>
    <!-- 模态框 -->
    </div>
</template>
<script>
import request from '@/utils/request'
import { get, del, post } from '@/utils/request'
import qs from 'querystring'
export default {
    data(){
        return{
            privileges:[], 
            paretnPrivileges:[],     
            form: {},
            visible: false,
            title: '添加权限',
            rules: {
                name: [
                { required: true, message: '请输入权限名称', trigger: 'change' }
                ],
                route: [
                { required: true, message: '请输入权限路径', trigger: 'change' }
                ],
                type: [
                { required: true, message: '请选择权限类型', trigger: 'change' }
                ]
            }
        }
    },
    created(){
        this.loadPrivileges();
        // this.loadParentPrivileges();
    },
    methods:{
        //加载权限信息
        loadPrivileges(){
            request.get("/privilege/findAllWithChildren")
            .then(response=>{
                this.privileges = response.data;
            })
        },
        //修改权限
        toEdit(record){
            this.loadParentPrivileges(record.id);
            this.form = record
            this.visible = true
        },
        //提交修改
        submitHandler() {
            // request.request({
            //     url:'/privilege/saveOrUpdate',
            //     method:'post',
            //     headers:{
            //         'Content-Type':'application/x-www-form-urlencoded'
            //     },
            //     data:qs.stringify(this.form)
                //
                this.$refs['privilege_form'].validate((valid) => {
                    if (valid) {
                    const url = '/privilege/saveOrUpdate'
                    post(url, this.form)
                        .then(response => {
                        this.visible = false
                        this.$message({ message: response.message, type: 'success' })
                        this.loadPrivileges()
                        })
                    } else {
                    return false
                    }
                })
        },

        //加载一级权限信息
        loadParentPrivileges(id){
            request.get("/privilege/findParentPrivilege?id="+id)
            .then(response=>{
                this.paretnPrivileges = response.data;
            })
        },
    }
}
</script>