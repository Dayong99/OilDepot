<template>
  <!-- <div class="app-container" style="background:#277A93;"> -->
          <!-- <div class="app-container" :style="{backgroundImage: 'url(' + bg + ')' }"> -->
          <div class="app-container">

      <el-row :gutter="24">
          <el-col :span="6" v-for="(item,index) in tankList" :key="index">
              <crane :info="item" :index="index" />
          </el-col>
      </el-row>
  </div>
</template>

<script>
import Crane from './component/crane'
import tank from '@/api/dynamic'
import bg from '@/image/1.png'

export default {
    components: { 
      Crane:Crane
    },
    data(){
      return {
        tankList:[],
        bg:bg
      }
    },
    beforeDestroy() {
    },
    created() {
      this.getData()
    },
    methods:{
      getData(){
        console.log(111)
        tank.workStatus().then((r) => {
            if (r.code === 20000) {
              this.tankList = r.data
            }
        }).catch(() => {
            this.$message.error('获取储罐工作状态信息失败') 
        })
    }
    }
}
</script>

<style scoped>
</style>