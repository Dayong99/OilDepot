<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import { debounce } from '@/utils'
import home from '@/api/home'

const animationDuration = 6000
var path = 'path://M2.2,0l25.5,0C29,0,30,1,30,2.2v0c0,1.2-1,2.2-2.2,2.2H2.2C1,4.5,0,3.5,0,2.2v0C0,1,1,0,2.2,0z';

export default {
  props: {
    className: {
      type: String,
      default: 'chart'
    },
    width: {
      type: String,
      default: '100%'
    },
    height: {
      type: String,
      default: '200px'
    }
  },
  data() {
    return {
      chart: null,
      max:null,
      capacity :[],
      name:[],
      tankData:[],
      showData:[]
    }
  },
  mounted() {
    this.initChart()
    this.__resizeHandler = debounce(() => {
      if (this.chart) {
        this.chart.resize()
      }
    }, 100)
    window.addEventListener('resize', this.__resizeHandler)
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    window.removeEventListener('resize', this.__resizeHandler)
    this.chart.dispose()
    this.chart = null
  },
  methods: {
    initChart() {
      home.tankTotal().then((r) => {
        if (r.code === 20000) {
          r.data.forEach((item,index)=>{
            this.name.push(item.tankName)
            this.tankData.push(item.oilStock)
            this.capacity.push(item.safeCapacity)
          })
          this.max = this.capacity.reduce(function(a , b){
            return b > a ? b : a;
          });
          // const m = this.max/18
          // console.log(m)
          this.tankData.forEach((item,index)=>{
            this.showData.push((item*100)/this.max)
          })
          const data = this.tankData
          this.chart = echarts.init(this.$el, 'macarons')

          this.chart.setOption({
            color: ['#0082ff', '#93a3b7'],
            grid:{
              top:20,
              bottom:'1%',
              left:0,
              right:0,
              containLabel: true
            },
            xAxis: {
                data: this.name,
                axisLabel: {
                          show: true,
                            textStyle: {
                              color: '#ffffff',  //更改坐标轴文字颜色
                              fontSize : 14      //更改坐标轴文字大小
                            },
                            margin:15
                        },
                axisLine:{
                  show:false
                },
                axisTick:{     
                  show:false
                }
            },
            yAxis: {
              show:false,
                max: 100,
                splitArea: {show: false},
                splitLine:{
                  show:false
                }
            },
            series: [{
                type: 'pictorialBar',
                name: 'realValue',
                symbol: path,
                z: 20,
                symbolBoundingData: 100,
                symbolSize: [33, 3],
                symbolMargin:3,
                symbolClip: true,
                symbolRepeat: true,
                data:this.showData
            }, {
                type: 'pictorialBar',
                name: 'background',
                symbol: path,
                symbolBoundingData: 100,
                symbolSize: [33,3],
                symbolMargin:3,
                symbolRepeat: 'fixed',
                animationDuration: 0,
                label: {
                  normal: {
                    show: true,
                    position: 'outside',
                    formatter: function (value) {
                      let index = value.dataIndex;
                      return data[index];
                    },
                    textStyle: {
                        color: '#ffffff',
                        fontSize:12
                    }
                }
            },
            data:this.showData
        }]
          })
        }
      }).catch(() => {
        this.$message.error('获取当前罐存总量信息失败')
      })
      
    }
  }
}
</script>
