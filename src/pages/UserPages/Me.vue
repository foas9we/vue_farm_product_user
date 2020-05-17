<template>
    <div class="myname">
        {{this.name}}<br/>
        {{form}}
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
</template>
<script>
import { mapGetters } from 'vuex'
import request from '@/utils/request'
import qs from 'qs'
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
            }
        }
    },
    computed: {
    ...mapGetters([
      'name',
    ])
  },
  created(){
      this.loadUser(this.name);
  },
  methods:{
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