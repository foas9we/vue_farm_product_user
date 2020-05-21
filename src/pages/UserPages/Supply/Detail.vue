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
     <h2 class="p-evaluate">产品评论</h2>{{$store.state.count}}
     <div class="tab-con">
         
         <!-- {{evaluate}} -->
         <div class="comment-item" v-for="e in evaluate" >
             <div class="comment-column">
                 <div class="user-info">
                     <h1>{{e.user.name}}</h1>
                 </div>
                <p class="comment-con">{{e.content}}</p>
                <div class="pic-list"><img :src="e.picture"></div>
                <div class="date">{{e.date | formatDate}}</div>
             </div>

         </div>
     </div>

     <el-dialog :title="title" :visible.sync="product_visible">
         
            <el-form ref="product_form" :model="form" :rules="rules">
                <el-form-item label="收件人" label-width="80px" prop="name">
                <el-input v-model="form.name" autocomplete="off" />
                </el-form-item>
                <el-form-item label="联系电话" label-width="80px" prop="phone">
                <el-input v-model="form.phone" autocomplete="off" />
                </el-form-item>
                <el-form-item label="收件地址" label-width="80px" prop="address">
                    <el-input v-model="form.address" autocomplete="off" />
                </el-form-item>
                <el-form-item label="下单人编号" hidden="true" label-width="80px">
                    <el-input v-model="form.userId" autocomplete="off" />
                </el-form-item>
                <el-form-item label="下单农产品编号" hidden="true" label-width="80px">
                    <el-input v-model="form.productId" autocomplete="off" />
                </el-form-item>
                </el-form>
                <div slot="footer" class="dialog-footer">
                    <el-button size="small" @click="product_visible = false">取 消</el-button>
                    <el-button type="primary" size="small" @click="toSave">确 定</el-button>
                </div>

                <h4 style="text-align:center">产品信息</h4>
                <h3 style="text-align:center">{{product.title}}</h3>
                <h3 style="text-align:center">{{product.price}}元</h3>
            </el-dialog> 
         <!-- 下单模态框 -->
</div>
    
   
    
</template>
<script>
import request from '@/utils/request'
import {formatDate} from '../../myJS/date'
import store from '../../../store'
import { mapGetters } from 'vuex'
import { get, post, del } from '@/utils/request'
import qs from 'querystring'
export default {

    data(){
        return{
           product:{} ,
           evaluate:{},
           product_visible:false,
           title:'下单',
           rules: {
            name: [
            { required: true, message: '请输入收货人', trigger: 'change' }
            ],
            phone: [
            { required: true, message: '请输入联系电话', trigger: 'change' }
            ],
            address: [
            { required: true, message: '请输入收货地址 ', trigger: 'change' }
            ]
        },
        form:{},
        user:{},
           
        }
   },
   filters: {
    formatDate(time) {
      var date = new Date(time);
      return formatDate(date, 'yyyy-MM-dd hh:mm');
    }
  },
  computed: {
    ...mapGetters([
      'name',
    ])
  },
   created(){
      this.product = this.$route.query;
      this.loadEvaluate(this.$route.query.id);
      this.loadUser(this.name);
      
    
   },
   methods:{
     loadUser(name){
         request.get('/baseUser/findByName?name='+name)
            .then(response=>{
                this.user = response.data;
            })
     },
     toSave(){
         this.$refs['product_form'].validate((valid) => {
                    if (valid) {
                    const url = '/Order/createOrder'
                    post(url, this.form)
                        .then(response => {
                        this.product_visible = false
                        this.$message({ message: response.message, type: 'success' })
                        })
                    } else {
                    return false
                    }
                })
     },
     toBuy(product){
        
        this.form.userId = this.user.id;
        this.form.productId = product.id;
        this.product_visible=true; 
     },
      loadEvaluate(id){
        request.get('/evaluate/findByProductId?id='+id)
        .then(response=>{
            this.evaluate = response.data;
        })
      },
//       timestampToTime (row, column) {
//         var date = new Date(row)
//         var Y = date.getFullYear() + '-'
//         var M = (date.getMonth() + 1 < 10 ? '0' + (date.getMonth() + 1) : date.getMonth() + 1) + '-'
//         var D = date.getDate() + ' '
//         var h = date.getHours() + ':'
//         var m = date.getMinutes() + ':'
//         var s = date.getSeconds()
//         return Y + M + D + h + m + s
// }
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

    .tab-con{
        padding: 10px 0;
        padding-top: 10px;
        padding-right: 0px;
        padding-bottom: 10px;
        padding-left: 0px;

        margin-top: 0px;
        margin-right: 0px;
        margin-bottom: 0px;
        margin-left: 0px;

        font: 12px/150% tahoma,arial,Microsoft YaHei,Hiragino Sans GB,"\u5b8b\u4f53",sans-serif;
        color: #666;
    }

    .comment-item{
        padding-top: 15px;
        padding-right: 15px;
        padding-bottom: 15px;
        padding-left: 15px;

        border-bottom-color: rgb(221, 221, 221);
        border-bottom-style: solid;
        border-bottom-width: 1px;

        margin-top: 0px;
        margin-right: 0px;
        margin-bottom: 0px;
        margin-left: 0px;

        font: 12px/150% tahoma,arial,Microsoft YaHei,Hiragino Sans GB,"\u5b8b\u4f53",sans-serif;
        font-style: normal;
        font-variant-caps: normal;
        font-weight: normal;
        font-stretch: normal;
        font-size: 12px;
        line-height: 150%;
        font-family: tahoma, arial, Microsoft YaHei, Hiragino Sans GB, "u5b8bu4f53", sans-serif;
        font-size-adjust: none;
        font-kerning: auto;
        font-optical-sizing: auto;
        font-variant-alternates: normal;
        font-variant-east-asian: normal;
        font-variant-ligatures: normal;
        font-variant-numeric: normal;
        font-variant-position: normal;
        font-language-override: normal;
        font-feature-settings: normal;
        font-variation-settings: normal;
        color: #666;
    }
    .comment-item{
        margin-left: 150px;
        margin: 0;
        padding: 0;
        font: 12px/150% tahoma,arial,Microsoft YaHei,Hiragino Sans GB,"\u5b8b\u4f53",sans-serif;
        color: #666;
    }
    .user-info{
        white-space: nowrap;
        text-overflow: ellipsis;
        overflow: hidden;
        overflow-x: hidden;
        overflow-y: hidden;
    }

    .comment-con{
            font: 12px/150% tahoma,arial,Microsoft YaHei,Hiragino Sans GB,"\u5b8b\u4f53",sans-serif;
    
    }
    .pic-list{
        margin: 0;
        padding: 0;
        font: 12px/150% tahoma,arial,Microsoft YaHei,Hiragino Sans GB,"\u5b8b\u4f53",sans-serif;
        color: #666;
    }
    .p-evaluate{
        text-align: center;
    }
</style>