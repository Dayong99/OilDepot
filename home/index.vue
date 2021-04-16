<template>
  <div class="main-div" :style="{backgroundImage: 'url(' + bg + ')' }">
    <el-row :gutter="24" style="width:1800px;">
      <el-col :span="12">
        <el-row :gutter="24">
          <div class="box box1">
            <div class="title">当前库存</div>
            <repertory />
          </div>
          <div class="box box1" style="float:right;">
            <div class="title">今日看板</div>
            <board />
          </div>
        </el-row>
        <el-row :gutter="24">
          <div class="box box1">
            <div class="title">主油库存总量</div>
            <div class="chartBox" style="padding:20px 5px;">
              <pie-chart></pie-chart>
            </div>
          </div>
          <div class="box box1" style="float:right;">
            <div class="title">当前主油消耗量</div>
            <div class="chartBox">
              <div class="date">
                <el-date-picker
                  v-model="time"
                  type="date"
                  style="width:140px;opacity:0;z-index:9999;position:absolute;right:0;cursor:pointer;"
                  placeholder="选择日期"
                  :clearable="false"
                  value-format="yyyy-MM-dd">
                </el-date-picker>
                <i class="el-icon-date"></i>
                <span>{{ time }}</span>
              </div>
              <bar-chart :time="time"></bar-chart>
            </div>
          </div>
        </el-row>
      </el-col>
      <el-col :span="12">
        <div class="box box4" style="float:right;">
          <div class="title">罐区状态</div>
          <div class="chartBox">
            <tank-area></tank-area>
          </div>
        </div>
      </el-col>
    </el-row>
    
    <el-row :gutter="24">
      <div class="box box3" >
        <div class="title">当前罐存总量</div>
        <div class="chartBox">
          <tank-total></tank-total>
        </div>
      </div>
      <div class="box box5">
        <div class="title" style="width:140px;">本年油料消耗历史</div>
        <div class="chartBox">
          <line-chart></line-chart>
        </div>
      </div>
    </el-row>
  </div>
</template>

<script>
import bg from '@/views/home/dashboard/bg.png'

import LineChart from './component/LineChart'
import BarChart from './component/BarChart'
import Board from './component/Board'
import TankTotal from './component/component/TankBar'
import Repertory from './component/Repertory'
import PieChart from './component/PieChart'
import TankArea from './component/TankArea'


export default {
  components: { 
      LineChart:LineChart , 
      BarChart:BarChart,
      Board:Board,
      TankTotal:TankTotal,
      Repertory:Repertory,
      PieChart:PieChart,
      TankArea:TankArea
    },
    data(){
      return {
        bg:bg,
        false:false,
        time:null
      }
    },
    beforeDestroy() {
      this.$store.commit('settings/CHANGE_SETTING', { key: 'showSettings', value: true })
    },
    created() {
      this.$store.dispatch('app/closeSideBar', { withoutAnimation: false })
      let date = new Date()
      this.time = date.getFullYear()+'-'+((date.getMonth()+1).toString().length<2?'0'+(date.getMonth()+1):(date.getMonth()+1))+'-'+(date.getDate().toString().length<2?'0'+date.getDate():date.getDate())
    },
    methods:{
    }
}
</script>

<style scoped>
.appBox {
  width:calc(100%-70px);
  height:100vh;
  background-size:100% 100%;
  background:url('dashboard/bg.png') no-repeat;
  padding:100px 20px 0;
}
.main-div{
  background-size:cover;
  position: fixed;
  top: 0;
  height: 100vh;
  width: 98%;
  padding:120px 2%;
}
.box {
  width:100%;
  background: rgba(22,50,86,0.8);
  border:2px solid #1f4b7d;
  border-radius:25px;
  position:relative;
  margin-bottom:40px;
  float:left;
}
/* .box1 {
  height:246px;
}
.box2 {
  height:293px;
}
.box3 {
  height:317px;
}
.box4 {
  height:575px;
}
.box5 {
  height:317px;
} */
.box1 {
  width:430px;
  height:220px;
}

.box4 {
  width:850px;
  height:481px;
}
.box3 {
  width:900px;
  height:260px;
}
.box5 {
  width:850px;
  height:260px;
  margin-left:40px;
}
.title {
  width:126px;
  height:33px;
  font-size:14px;
  color:white;
  letter-spacing:1px;
  position: absolute;
  top:-21px;
  left:50%;
  margin-left:-63px;
  line-height:33px;
  text-align:center;
  background:url('dashboard/title_bg.png') no-repeat;
  background-size: 100% 33px;
}
.chartBox {
  padding:30px;
}

.date {
  color:white;
  font-size:16px;
  position:absolute;
  right:30px;
}
.date i {
  margin-right:5px;
}
.repertoryBox {
  display:-webkit-flex;
  display:flex;
  justify-content: space-around;
}

</style>