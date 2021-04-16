<template>
  <div class="svgBox">
    <!-- <div class="number">{{info.oilNum}}</div> -->
    <div class="infoBox">
      <el-row :gutter="24">
        <el-col :span="12">
            <p>
              <span>鹤位名称:</span>{{index+1}}号鹤位
            </p>
            <p>
              <span>高度(m):</span>
              <count-to :start-val="0" :end-val="info.oilHeight" :duration="2600" />
            </p>
            <p>
              <span>温度(℃):</span>
              <count-to :start-val="0" :end-val="5" :duration="2600" />
            </p>
            <p>
              <span>今日装车总量:</span>
              <count-to :start-val="0" :end-val="info.oilStock" :duration="2600" />
            </p>
            <p>
              <span>报警状态:</span>
              <span v-show="index%2===0">安全</span>
              <span v-show="index%2!==0">
                <svg-icon icon-class="alarm"  class="alarm animated flash infinite slow" />报警
              </span>
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
      this.info.oilHeight=100
      this.alarmHt = this.info.oilHeight
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
    background:url('../crane.png') no-repeat 40px 35px;
    background-size: 150px 120px;
    text-align:center;
    position: relative;
}
.number {
    width: 30px;
    height: 30px;
    background: #409EFF;
    -moz-border-radius:10px;
    -webkit-border-radius: 15px;
    border-radius: 15px;
    line-height:30px;
    display:inline-block;
    font-size:18px;
    font-weight:bold;
    color:white;
    position: absolute;
    left:90px;
    top:80px;
}
.infoBox {
    position: absolute;
    left:200px;
    margin-top:20px;
    font-size:14px;
    color:black;
}
.infoBox p {
    width:200px;
    text-align: left;
    margin:10px 0;
}
.infoBox .el-col p span:first-child {
  width:80px;
  display: inline-block;
  font-weight:bold;
}
.infoBox .el-col:last-child p span:first-child {
  width:120px;
  display: inline-block;
  font-weight:bold;
  text-align: right;
  padding-right:8px;
}
.svg-icon {
  margin-right:5px;
  color:#FF0000;
}
</style>