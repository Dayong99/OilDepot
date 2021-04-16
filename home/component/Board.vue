<template>
    <div class="board">
                <div class="board_title">入库(kg)</div>
                <el-row :gutter="24">
                  <el-col :span="12">
                    <span>卸油:</span>{{ info.unloading.toFixed(3) }}
                    <!-- <span class="unit">kg</span> -->
                  </el-col>
                  <el-col :span="12">
                    <span>退油:</span>{{ info.returnOil.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                </el-row>
                <div class="board_title">出库(kg)</div>
                <el-row :gutter="24">
                  <el-col :span="12">
                    <span>飞机加油:</span>{{ info.aircraftRefuel.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                  <el-col :span="12">
                    <span>槽车加油:</span>{{ info.tankRefuel.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                  <el-col :span="12">
                    <span>零发油:</span>{{ info.zeroOil.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                  <el-col :span="12">
                    <span>地面加油:</span>{{ info.groundRefuel.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                  <el-col :span="12">
                    <span>扫雪吹沙:</span>{{ info.snowoil.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                </el-row>
                <div class="board_title">周转(kg)</div>
                <el-row :gutter="24">
                  <el-col :span="12">
                    <span>井房加油:</span>{{ info.refueling.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                  <el-col :span="12">
                    <span>倒罐:</span>{{ info.pour.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                </el-row>
                <el-row :gutter="24">
                  <el-col :span="12">
                    <span>发油:</span>{{ info.hairOil.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                  <el-col :span="12">
                    <span>输送三号喷气燃料:</span>{{ info.converyNoJet.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                </el-row>
                <el-row :gutter="24">
                  <el-col :span="12">
                    <span>加油车退油:</span>{{ info.returnoil1.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                  <el-col :span="12">
                    <span>退油车退油:</span>{{ info.returnoil2.toFixed(3) }}
                     <!-- <span class="unit">kg</span> -->
                  </el-col>
                </el-row>
              </div>
</template>

<script>
import home from '@/api/home'

export default {
  data() {
    return {
      info:{
        aircraftRefuel: 0,
        converyNoJet: 0,
        groundRefuel: 0,
        hairOil: 0,
        pour: 0,
        refueling: 0,
        returnOil: 0,
        returnoil1: 0,
        returnoil2: 0,
        tankRefuel: 0,
        unloading: 0,
        zeroOil: 0,
        snowoil:0
      }
    }
  },
  computed: {
  },
  created() {
    this.getData()
  },
  methods: {
    getData(){
      home.getBoard().then((r) => {
        this.info = r.data.info
        console.log(this.info.unloading)
      }).catch(e => {
        this.$message.error('获取今日看板信息失败')
      })
    }
  }
}
</script>

<style scoped>
.board{
  padding:5px 12px;
}
.board .board_title {
  font-size:14px;
  font-weight:bold;
  color:'#ffffff';
  margin:5px 0; 
  color:white;
  letter-spacing: 2px;
}
.board .board_title:first-child {
  margin-top:10px;
}
.board .el-col {
  font-size:12px;
  color:rgba(0,246,255);
  margin-bottom:5px;
  letter-spacing: 1px;
}
.board span {
  color:white;
  padding-right:5px;
}
.board .unit {
    color:rgba(0,246,255);
  margin-left:1px;
}
</style>