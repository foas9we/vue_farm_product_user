<template>
   
    
</template>
<script>
import request from '@/utils/request'
export default {
    data(){
        return{
            
        }
   },

   created(){
      this.reloadProduct();
      
   },
   methods:{
       reloadProduct(){
                request.get('/product/findAllProduct')
        .then(result=>{
            this.products = result.data;
            this.options = result.data;
        })
       },
   }
}
</script>
<style scoped>
</style>