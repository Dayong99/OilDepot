<template>
  <!-- <div class="app-container" style="background:#277A93;"> -->
          <div class="app-container" :style="{backgroundImage: 'url(' + bg + ')' }">
      <el-row :gutter="24">
          <el-col :span="12" v-for="(item,index) in tankList" :key="index">
              <work :info="item" :index="index" :oil="oilOption" :area="areaOption" :build="buildOption"></work>
          </el-col>
      </el-row>
  </div>
</template>

<script>
import Work from './component/tank'
import tank from '@/api/dynamic'
import bg from '@/image/1.png'

export default {
    components: { 
      Work:Work
    },
    data(){
      return {
        tankList:[],
        bg:bg,
        oilOption:[],
        areaOption:[],
        buildOption:[]
      }
    },
    beforeDestroy() {
    },
    created() {
      this.getData()
      this.getOil().then(data => {
        this.oilOption = data
      })
      this.getArea().then(data => {
        this.areaOption = data
      })
      this.getBuilding().then(data => {
        this.buildOption = data
      }) 
    },
    methods:{
      getData(){
        tank.tankLevel().then((r) => {
            if (r.code === 20000) {
              r.data.forEach((item,index) => {
                if((item.oilNum>=8)&&(item.oilNum<=16)){
                  this.tankList.push(item)
                }
              });
              // this.tankList = r.data
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