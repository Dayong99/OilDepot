<template>
  <div class="app-container">
    <el-row :gutter="20">
      <el-col :span="18">
        <div class="block">
          <el-timeline>
            <el-timeline-item :color="color2">
              <span class="title">推送基础数据</span>
              <el-progress :percentage="pushData.percent" :color="color2" />
              <span class="content">{{ pushData.title }}</span>
            </el-timeline-item>
            <el-timeline-item :color="color5">
              <span class="title">下发人员信息</span>
              <el-progress :percentage="pull.percent" :color="color5" />
              <span class="content">{{ pull.title }}</span>
            </el-timeline-item>
            <el-timeline-item :color="color6">
              <span class="title">拉取人员信息</span>
              <el-progress :percentage="push.percent" :color="color6" />
              <span class="content">{{ push.title }}</span>
            </el-timeline-item>
            <el-timeline-item :color="color1">
              <span class="title">拉取零发油信息</span>
              <el-progress :percentage="oilInfo.percent" :color="color1" />
              <span class="content">{{ oilInfo.title }}</span>
            </el-timeline-item>
            <el-timeline-item :color="color3">
              <span class="title">拉取槽车订单信息</span>
              <el-progress :percentage="carInfo.percent" :color="color3" />
              <span class="content">{{ carInfo.title }}</span>
            </el-timeline-item>
            <el-timeline-item :color="color">
              <span class="title">拉取退油订单信息</span>
              <el-progress :percentage="returnInfo.percent" :color="color" />
              <span class="content">{{ returnInfo.title }}</span>
            </el-timeline-item>
            <el-timeline-item :color="color4">
              <span class="title">零发油人员数据</span>
              <el-progress :percentage="oilPerson.percent" :color="color4" />
              <span class="content">{{ oilPerson.title }}</span>
            </el-timeline-item>
            <el-timeline-item :color="color7">
              <span class="title">零发油基础数据</span>
              <el-progress :percentage="oilDatas.percent" :color="color7" />
              <span class="content">{{ oilDatas.title }}</span>
            </el-timeline-item>
          </el-timeline>
        </div>
      </el-col>
      <el-col :span="6">
        <el-container class="buttons">
          <!-- <el-header>
            <el-button type="success" icon="el-icon-tickets" @click="pushUserInfo">基础数据</el-button>
          </el-header> -->
          <el-header>
            <el-button type="warning" icon="el-icon-tickets" @click="PcToAndroidInfo">推送基础数据</el-button>
          </el-header>
          <el-header>
            <el-button type="success" icon="el-icon-user" style="background:#6B6BF5;border-color:#6B6BF5;color:white;" @click="pushUserInfo">下发人员信息</el-button>
          </el-header>
          <el-header>
            <el-button type="success" icon="el-icon-user" style="background:#F56BB0;border-color:#F56BB0;color:white;" @click="pullUserInfo">拉取人员信息</el-button>
          </el-header>
          <el-header>
            <el-button style="background:rgb(169,150,212); border-color:rgb(169,150,212);color:white" icon="el-icon-tickets" @click="spoFaceData">拉取零发油信息</el-button>
          </el-header>
          <el-footer>
            <el-button type="primary" icon="el-icon-tickets" @click="tankFaceData">拉取槽车订单信息</el-button>
          </el-footer>
          <el-footer>
            <el-button type="success" icon="el-icon-tickets" @click="returnFaceData">拉取退油订单信息</el-button>
          </el-footer>
          <el-footer>
            <el-button style="background:rgb(240,109,105); border-color:rgb(240,109,105);color:white" icon="el-icon-tickets" @click="oilPersonData">零发油人员数据</el-button>
          </el-footer>
          <el-footer>
            <el-button style="background:#FBB814;border-color:#FBB814;color:white;" icon="el-icon-tickets" @click="getoilData">零发油基础数据</el-button>
          </el-footer>
        </el-container>
      </el-col>
    </el-row>

  </div>

</template>

<script>
import { getStatus, pushUserInfo, pullUserInfo, spoFaceData, PcToAndroidInfo, tankFaceData, returnFaceData , oilPersonData , oilData } from '@/api/systemManagement/dataSync'

export default {
  data() {
    return {
      timer: null,
      color: '#0bbd87',
      color1: 'rgb(169,150,212)',
      color2: '#EBB563',
      color3: 'rgb(64,158,255)',
      color4: 'rgb(245,108,108)',
      color5: '#6B6BF5',
      color6: '#F56BB0',
       color7: '#FBB814',
      number: 0,
      pushData: {
        order: 0,
        percent: 0,
        title: '',
        type: '1'
      },
      pull: {
        order: 0,
        percent: 0,
        title: '',
        type: '4'
      },
      push: {
        order: 0,
        percent: 0,
        title: '',
        type: '3'
      },
      oilInfo: {
        order: 0,
        percent: 0,
        title: '',
        type: '5'
      },
      carInfo: {
        order: 0,
        percent: 0,
        title: '',
        type: '2'
      },
      returnInfo: {
        order: 0,
        percent: 0,
        title: '',
        type: '6'
      },
      oilPerson: {
        order: 0,
        percent: 0,
        title: '',
        type: '7'
      },
      oilDatas: {
        order: 0,
        percent: 0,
        title: '',
        type: '8'
      }
      // websock:null
    }
  },
  created() {
    this.initWebSocket()
  },
  //  destroyed() {
  //     this.websock.close() //离开路由之后断开websocket连接
  //   },
  methods: {
    PcToAndroidInfo() {
      this.play()
      PcToAndroidInfo()
    },
    pushUserInfo() {
      this.play()
      pushUserInfo()
    },
    pullUserInfo() {
      this.play()
      pullUserInfo()
    },
    spoFaceData() {
      this.play()
      spoFaceData()
    },
    tankFaceData() {
      this.play()
      tankFaceData()
    },

    returnFaceData() {
      this.play()
      returnFaceData()
    },
    oilPersonData() {
      this.play()
      oilPersonData()
    },
    getoilData() {
      this.play()
      oilData()
    },
    reSet() {
      this.number = 0
      this.pushData = {
        order: 0,
        percent: 0,
        title: '',
        type: '1'
      }
      this.pull = {
        order: 0,
        percent: 0,
        title: '',
        type: '4'
      }
      this.push = {
        order: 0,
        percent: 0,
        title: '',
        type: '3'
      }
      this.oilInfo = {
        order: 0,
        percent: 0,
        title: '',
        type: '5'
      }

      this.carInfo = {
        order: 0,
        percent: 0,
        title: '',
        type: '2'
      }
      this.returnInfo = {
        order: 0,
        percent: 0,
        title: '',
        type: '6'
      }
      this.oilPerson = {
        order: 0,
        percent: 0,
        title: '',
        type: '7'
      }
      this.oilDatas = {
        order: 0,
        percent: 0,
        title: '',
        type: '8'
      }
    },
    initWebSocket() {
      if (typeof (WebSocket) !== 'undefined') {
        this.websock = new WebSocket(process.env.VUE_APP_WS_API + '/ws/depot/121')
        this.websock.onmessage = this.websocketonmessage
        this.websock.onopen = this.websocketonopen
        this.websock.onerror = this.websocketonerror
        this.websock.onclose = this.websocketclose
      }
    },
    websocketonopen() { // 连接建立之后执行send方法发送数据
      console.log('websocketonopen')
    },
    websocketonerror() { // 连接建立失败重连
      this.initWebSocket()
    },
    websocketonmessage(r) {
      console.log('数据接收')
      console.log(r)
      if (r.data === '连接成功') {
        return
      }
      if (r.data) {
         var data= JSON.parse(r.data);
        if (data.type === 1) {
          this.pushData.order = data.order
          this.pushData.percent = data.percent
          this.pushData.title = data.title
          if(this.pushData.percent===100){
            this.pushData.title = '完成'
          }
        } else if (data.type === 4) {
          this.pull.order = data.order
          this.pull.percent = data.percent
          this.pull.title = data.title
          if(this.pull.percent===100){
            this.pull.title = '完成'
          }
        } else if (data.type === 3) {
          this.push.order = data.order
          this.push.percent = data.percent
          this.push.title = data.title
          if(this.push.percent===100){
            this.push.title = '完成'
          }
        } else if (data.type === 5) {
          this.oilInfo.order = data.order
          this.oilInfo.percent = data.percent
          this.oilInfo.title = data.title
          if(this.oilInfo.percent===100){
            this.oilInfo.title = '完成'
          }
        } else if (data.type === 2) {
          this.carInfo.order = data.order
          this.carInfo.percent = data.percent
          this.carInfo.title = data.title
          if(this.carInfo.percent===100){
            this.carInfo.title = '完成'
          }
        } else if (data.type === 6) {
          this.returnInfo.order = data.order
          this.returnInfo.percent = data.percent
          this.returnInfo.title = data.title
          if(this.returnInfo.percent===100){
            this.returnInfo.title = '完成'
          }
        } else if (data.type === 7) {
          this.oilPerson.order = data.order
          this.oilPerson.percent = data.percent
          this.oilPerson.title = data.title
          if(this.oilPerson.percent===100){
            this.oilPerson.title = '完成'
          }
        } else if (data.type === 8) {
          this.oilDatas.order = data.order
          this.oilDatas.percent = data.percent
          this.oilDatas.title = data.title
          if(this.oilDatas.percent===100){
            this.oilDatas.title = '完成'
          }
        }
      }
    },
    websocketsend(Data) { // 数据发送
      this.websock.send(Data)
    },
    websocketclose(e) { // 关闭
      console.log('断开连接', e)
    },
    play() {
      this.reSet()
      // setTimeout(() => {
      //   this.timer = setInterval(() => {
      //     this.getStatus()
      //   }, 1000)
      // }, 8000)
    },
    stop() {
      if (this.timer) {
        clearInterval(this.timer)
      }
    }
  }
}
</script>

<style scoped>
.title {
  font-size:16px;
  font-weight:bold;
  display: inline-block;
  margin-bottom:5px
}
.content {
  font-size:14px;
  line-height:30px;
}
.buttons{
   margin-top: 25px;
}
.el-button {
  margin:10px 0;
}
</style>

