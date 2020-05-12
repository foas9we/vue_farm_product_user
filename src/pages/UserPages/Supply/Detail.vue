<template>
<div>
    <div class="detail">
      
         <!-- 商品图片、名称、价格 -->
            <div class="product-image"><br/>
                <img :src="product.media">
            </div>
            <div class="product-info">
                <h1 class="product-name">{{product.title}}</h1>
                <div class="product-cost">￥{{product.price}}</div>
                <div class="product-add-cart" @click="toBuy(product)">现在下单</div><br/><br/>
            </div>
            
    </div>
    <div class="product-desc">
                <h2>产品介绍</h2>
                卖家:{{product.user.name}}&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp&nbsp
                发货地：{{product.user.address}}<br/>
                {{product.description}}
                <img :src="product.media">
     </div>
     <div >
         <h2>产品评论</h2>
         {{evaluate}}
     </div>

     <!-- <el-dialog :title="title" :visible.sync="product_visible">
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
            </el-dialog> --> -->
         <!-- 新建用户模态框 -->
</div>
    
   
    
</template>
<script>
import request from '@/utils/request'
export default {
    data(){
        return{
           product:{} ,
           evaluate:{},
        //    product_visible:false,
        //    title:'下单',
        //    rules: {
        //     name: [
        //     { required: true, message: '请输入用户名', trigger: 'change' }
        //     ],
        //     password: [
        //     { required: true, message: '请输入密码', trigger: 'change' }
        //     ],
        //     gender: [
        //     { required: true, message: '请选择性别', trigger: 'change' }
        //     ]
        // }
           
        }
   },

   created(){
      this.product = this.$route.query;
      this.loadEvaluate(this.product.id);
    
   },
   methods:{
      loadEvaluate(id){
        request.get('/evaluate/findByProductId?id='+id)
        .then(response=>{
            this.evaluate = response.data;
        })
      }
   }
}
</script>
<style scoped>
    .detail{
        margin: 32px;
        padding: 32px;
        background: #fff;
        border: 1px solid #dddee1;
        border-radius: 10px;
        overflow: hidden;
    }
    .product-image{
        width: 50%;
        height: 550px;
        float: left;
        text-align: center;
    }
    .product-image img{
        height: 100%;
    }
    .product-info{
        width: 50%;
        padding: 150px 0 250px;
        height: 150px;
        float: left;
        text-align: center;
    }
    .product-cost{
        color: #f2352e;
        margin: 8px 0;
    }
    .product-add-cart{
        display: inline-block;
        padding: 8px 64px;
        margin: 8px 0;
        background: #2d8cf0;
        color: #fff;
        border-radius: 4px;
        cursor: pointer;
    }
    .product-desc{
        background: #fff;
        margin: 32px;
        padding: 32px;
        border: 1px solid #dddee1;
        border-radius: 10px;
        text-align: center;
    }
    .product-desc img{
        display: block;
        width: 50%;
        margin: 32px auto;
        padding: 32px;
        border-bottom: 1px solid #dddee1;
    }
</style>