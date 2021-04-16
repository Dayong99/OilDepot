<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import { debounce } from '@/utils'
import home from '@/api/home'

const animationDuration = 6000

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
    },
    time:{
      type:String
    }
  },
  data() {
    return {
      chart: null,
      data:[],
      name:[]
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
              console.log(this.time)

        if (r.code === 20000) {
          r.data.forEach((item,index)=>{
            if((item.oilNum>=8)&&(item.oilNum<=16)){
              console.log(item.oilNum)
              this.name.push(item.tankName)
              this.data.push(item.oilStock)
            }
          })
          this.chart = echarts.init(this.$el, 'macarons')

          this.chart.setOption({
            color: ['#5ec1e5'],
            tooltip : {
                trigger: 'axis',
                axisPointer : {            // 坐标轴指示器，坐标轴触发有效
                    type : 'shadow'        // 默认为直线，可选为：'line' | 'shadow'
                }
            },
            grid:{
              top:'25',
              bottom:'1%',
              left:0,
              right:0,
              containLabel: true
            },
            xAxis : [
                {
                    type : 'category',
                    data : this.name,
                    axisTick: {
                        show:false,
                        alignWithLabel: true
                    },
                    axisLine: {
                        lineStyle: {
                            type: 'solid',
                            color: '#4f7399',
                            width:'2'
                        }
                    },
                        axisLabel: {
                          show: true,
                          interval:0,
                          // rotate:40,
                            textStyle: {
                              color: '#ffffff',  //更改坐标轴文字颜色
                              fontSize : 12      //更改坐标轴文字大小
                            },
                            margin:15
                        }
                }
            ],
            yAxis : [
                {
                    type : 'value',
                    axisTick: {
                      show: false
                    },
                    splitLine:{
                      show:true,
                      lineStyle:{
                        type:'solid',
                        color:'#4f7399'
                      }     
                    },
                        splitArea : {show : false},//保留网格区域
                        axisLine: {
                            lineStyle: {
                                type: 'solid',
                                color:'#4f7399',
                                width:'2'
                            }
                        },
                        axisLabel:{
                          color:'#ffffff',
                            margin:15
                        }
                }
            ],
            series : [
                {
                    name:'罐存总量',
                    type:'bar',
                    barWidth: '40%',
                    data:this.data
                }
            ]
          })
        }
      }).catch(() => {
        this.$message.error('获取当前罐存总量信息失败')
      })
      
    }
  }
}
</script>
