<template>
  <div class="svgBox">
    <div class="level">
      <!-- <div :class="{ht:true}" :style="{background:'red',height:alarmHt+'px'}" class="animated flash infinite slow"></div> -->
      <div :class="{ht:true}" :style="{background:'red',height:alarmHt+'px'}"></div>
    </div>
    <div class="number">{{info.oilNum}}</div>
    <div class="infoBox">
      <el-row :gutter="24">
        <el-col :span="12">
            <p>
              <span>油罐名称:</span>{{info.tankName}}
            </p>
            <p>
              <span>油品名称:</span>{{showOil(info.oilType)}}
            </p>
            <p>
              <span>油面高度(m):</span>
              <count-to :start-val="0" :end-val="info.oilHeight" :duration="2600" :decimals="3"/>
            </p>
            <p>
              <span>重量(kg):</span>
              <count-to :start-val="0" :end-val="info.oilStock" :duration="2600" :decimals="3"/> 
            </p>
            <p>
              <span>密度:</span>
              <count-to :start-val="0" :end-val="info.oilDensity" :duration="2600" :decimals="3"/>
            </p>
            <p>
              <span>罐区域:</span><span v-text="showArea(info.areaId)"></span>
            </p>
            <p>
              <span>建筑形式:</span><span v-text="showBuild(info.buildingStyle)"></span>
            </p>
        </el-col>
        <el-col :span="12">
          <p>
            <span>公称容量(L):</span>
            <count-to :start-val="0" :end-val="info.nominalCapacity" :duration="2600" :decimals="3"/>
          </p>
          <p>
            <span>实际容量(L):</span>
            <count-to :start-val="0" :end-val="info.actualCapacity" :duration="2600" :decimals="3"/> 
          </p>
          <p>
            <span>最大装油高度(m):</span>
            <count-to :start-val="0" :end-val="info.maxHeight" :duration="2600" :decimals="3"/> 
          </p>
          <p>
            <span>安全容量(L):</span>
            <count-to :start-val="0" :end-val="info.safeCapacity" :duration="2600" :decimals="3"/> 
          </p>
          <p>
            <span>安全高度(m):</span>
            <count-to :start-val="0" :end-val="info.safeHeight" :duration="2600" :decimals="3"/> 
          </p>
          <p>
            <span>静态锁定值:</span>
            <count-to :start-val="0" :end-val="info.staticLockValue" :duration="2600" :decimals="3"/>
          </p>
          <p>
            <span>低液位报警:</span>
            <count-to :start-val="0" :end-val="info.lowValue" :duration="2600" :decimals="3"/>
          </p>
          <p>
            <span>高液位报警:</span>
            <count-to :start-val="0" :end-val="info.heightValue" :duration="2600" :decimals="3"/>
          </p>
        </el-col>
      </el-row>
    </div>
 </div>
</template>

<script>
import { watch } from "fs"

import CountTo from 'vue-count-to'

export default {
    name: 'Work',
    components: {
      CountTo
    },
    props: {
      info:{
        type:Object
      },
      index:{
        type:Number
      },
      oil:{
        type:Array
      },
      area:{
        type:Array
      },
      build:{
        type:Array
      }
    },
    data(){
      return {
        oilOption:[],
        areaOption: [],
        buildOption:[],
        showBg:true,
        alarmHt:0
      }
    },
    created() {
      // let height = document.getElementsByClassName('ht')
      // console.log(this.index)
      // console.log(height)
      // console.log(height[this.index])
      // this.info.oilHeight = 120
      // height[this.index].style.height = this.info.oilHeight + 'px'
      // this.$refs.ht.style.background='red'
    },
    beforeDestroy() {
    },
    mounted() {
      this.alarmHt = (this.info.oilHeight*140)/this.info.maxHeight
      this.oilOption = this.oil
      this.areaOption = this.area
      this.buildOption = this.build
    },
    methods:{
      showOil(val){
      for(let i=0;i<this.oilOption.length;i++){
        if (parseInt(this.oilOption[i].baseValue) === val) {
          return this.oilOption[i].baseKey
        }
      }
    },
      showArea(val){
      for(let i=0;i<this.areaOption.length;i++){
        if (parseInt(this.areaOption[i].baseValue) === val) {
          return this.areaOption[i].baseKey
        }
      }
    },
    showBuild(val){
      for(let i=0;i<this.buildOption.length;i++){
        if (parseInt(this.buildOption[i].baseValue) === parseInt(val)) {
          return this.buildOption[i].baseKey
        }
      }
    }
    }
}
</script>

<style lang="scss" scoped>
// $height: 120px;

.svgBox {
    width:100%;
    height:300px;
    background:url('../tank1.png') no-repeat 50px 20px;
    background-size: 150px 200px;
    text-align:center;
    position: relative;
}
.level {
  height:140px;
  width:10px;
  border:2px solid #909399;
  position:absolute;
  bottom:100px;
  left:30px;
}
// @keyframes go {
// 　from {height:0;}
// 	to {height:$height;}
// }
// .red {
//   background:red;
//   animation:go 3s infinite;
//   -webkit-animation:go 3s; /* Safari 和 Chrome */
// }

.level .ht {
  width:6px;
  /* background:red; */
  // height:$height;
  transition:height 4s;
  -moz-transition:height 4s; /* Firefox 4 */
  -webkit-transition:height 4s; /* Safari and Chrome */
  -o-transition:height 4s; /* Opera */
  position: absolute;
  bottom:0;
}
.number {
    width: 30px;
    height: 30px;
    background: #409EFF;
    -moz-border-radius:15px;
    -webkit-border-radius: 15px;
    border-radius: 15px;
    line-height:30px;
    display:inline-block;
    font-size:16px;
    font-weight:bold;
    color:white;
    position: absolute;
    left:110px;
    top:115px;
}
.infoBox {
    position: absolute;
    left:230px;
    margin-top:20px;
    font-size:14px;
    color:white;
}
.infoBox p {
    width:200px;
    text-align: left;
    margin:10px 0;
}
.infoBox .el-col p span:first-child {
  width:100px;
  display: inline-block;
  font-weight:bold;
  text-align:right;
  padding-right:8px;
}
.infoBox .el-col:last-child p span:first-child {
  width:120px;
  display: inline-block;
  font-weight:bold;
}
</style>