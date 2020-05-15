<template>
    <div class="myname">
        {{name}}
    </div>
</template>
<script>
import { mapGetters } from 'vuex'
export default {
    data(){
        return{

        }
    },
    computed: {
    ...mapGetters([
      'name',
    ])
  }
}
</script>