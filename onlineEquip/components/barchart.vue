<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import { debounce } from '@/utils'
import online from '@/api/onlineEquip'

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
      default: '600px'
    },
    times:{
      type:Array
    }
  },
  data() {
    return {
      chart: null,
        queryParam:{
          intervalBeginTime:null,
          intervalEndTime:null
        },
        name:[],
        num:[]
    }
  },
  created(){
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
  watch: {
    times(newVal,oldVal){
      console.log(newVal)
      if(newVal){
        this.queryParam.intervalBeginTime = newVal[0]
        this.queryParam.intervalEndTime = newVal[1]
        this.initChart()
      }else if(newVal===null){
        this.queryParam.intervalBeginTime = null
        this.queryParam.intervalEndTime = null
        this.initChart()
      }
    }
  },
  methods: {

    initChart() {
       online.statistic(this.queryParam).then((r) => {
        if (r.code === 20000) {
          this.name = []
          this.num = []
          r.data.forEach((item,index)=>{
            this.name.push(item.equipName)
            this.num.push(item.sums)
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
                            color: '#000000',
                            width:'1'
                        }
                    },
                        axisLabel: {
                          show: true,
                            textStyle: {
                              color: '#000000',  //更改坐标轴文字颜色
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
                      show:false,
                      lineStyle:{
                        type:'solid',
                        color:'#000000'
                      }
                    },
                        splitArea : {show : false},//保留网格区域
                        axisLine: {
                            lineStyle: {
                                type: 'solid',
                                color:'#000000',
                                width:'1'
                            }
                        },
                        axisLabel:{
                          color:'#000000',
                            margin:15
                        }
                }
            ],
            series : [
                {
                    name:'维修次数',
                    type:'bar',
                    barWidth: '20%',
                    data:this.num
                }
            ]
          })
        }
      }).catch(() => {
        this.$message.error('获取当前维修信息失败')
      })
      
    }
  }
}
</script>
