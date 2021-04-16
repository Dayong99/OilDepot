<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import { debounce } from '@/utils'
import home from '@/api/home'

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
      default: '220px'
    },
    autoResize: {
      type: Boolean,
      default: true
    }
  },
  data() {
    return {
      chart: null,
      sidebarElm: null,
      expectedData:[12,20,30,40,50,60,70],
      actualData:[16,26,6,45,57,53,90],
      time:['1月','2月','3月','4月','5月','6月','7月','8月','9月','10月','11月','12月'],
      oil1:[],
      oil2:[],
      oil3:[]
    }
  },
  watch: {
    chartData: {
      deep: true,
      handler(val) {
        this.setOptions(val)
      }
    }
  },
  mounted() {
    this.initChart()
    if (this.autoResize) {
      this.__resizeHandler = debounce(() => {
        if (this.chart) {
          this.chart.resize()
        }
      }, 100)
      window.addEventListener('resize', this.__resizeHandler)
    }

    // 监听侧边栏的变化
    this.sidebarElm = document.getElementsByClassName('sidebar-container')[0]
    this.sidebarElm && this.sidebarElm.addEventListener('transitionend', this.sidebarResizeHandler)
  },
  beforeDestroy() {
    if (!this.chart) {
      return
    }
    if (this.autoResize) {
      window.removeEventListener('resize', this.__resizeHandler)
    }

    this.sidebarElm && this.sidebarElm.removeEventListener('transitionend', this.sidebarResizeHandler)

    this.chart.dispose()
    this.chart = null
  },
  methods: {
    initChart() {
      home.history().then((r) => {
        if (r.code === 20000) {
          r.data.forEach((item,index)=>{
            this.time.forEach((item1,index1)=>{
              if(item.monthno+'月'===item1){
                if(item.oilType===0){
                  this.oil1[index1]=item.weight.toFixed(2)
                }else if(item.oilType===1){
                  this.oil2[index1]=item.weight.toFixed(2)
                }else if(item.oilType===2){
                  this.oil3[index1]=item.weight.toFixed(2)
                }
              }
            })
          })
          console.log(this.oil1)
          this.chart = echarts.init(this.$el, 'macarons')

          this.chart.setOption({
            grid:{
              bottom:'1%',
              left:0,
              right:'6%',
              containLabel: true
            },
            xAxis: {
              name:'月份',
              type:'category',
              nameTextStyle:{
                color:'#ffffff',
                fontSize:16,
                align:'left'
              },
              data: this.time,
              axisLine: {
                        lineStyle: {
                            type: 'solid',
                            color: '#8b97a5',
                            width:'2'
                        }
                    },
                    axisLabel: {
                          show: true,
                            textStyle: {
                              color: '#ffffff',  //更改坐标轴文字颜色
                              fontSize : 14      //更改坐标轴文字大小
                            },
                            margin:20
                        }
            },
            grid: {
              left: 10,
              right: '6%',
              bottom: 20,
              top: 30,
              containLabel: true
            },
            tooltip: {
              axisPointer: {
                type: 'axis'
              }
            },
            yAxis: {
              axisTick: {
                show: false
              },
              splitLine:{
                show:true,
                lineStyle:{
                  type:'dotted'
                }
              },
                  splitArea : {show : false},//保留网格区域
                  axisLine: {
                      lineStyle: {
                          type: 'solid',
                          color:'#8b97a5',
                          width:'2'
                      }
                  },
                  axisLabel: {
                          show: true,
                            textStyle: {
                              color: '#ffffff',  //更改坐标轴文字颜色
                              fontSize : 16      //更改坐标轴文字大小
                            },
                            margin:15
                        }
            },
            legend: {
              data: ['汽油', '柴油','航空3号喷气燃料油'],
              right:'6%',
              textStyle:{
                color:'white'
              },
              icon:'rect',
              itemWidth:20,
              itemHeight:10,
              itemGap:30
            },
            series: [{
              name: '汽油', itemStyle: {
                normal: {
                  color: '#ffb668',
                  lineStyle: {
                    color: '#ffb668',
                    width: 2
                  }
                }
              },
              type: 'line',
              data: this.oil1
            },
            {
              name: '柴油',
              type: 'line',
              itemStyle: {
                normal: {
                  color: '#84a4f4',
                  lineStyle: {
                    color: '#84a4f4',
                    width: 2
                  }
                }
              },
              data: this.oil2
            },
            {
              name: '航空3号喷气燃料油',
              type: 'line',
              itemStyle: {
                normal: {
                  color: '#85F49D',
                  lineStyle: {
                    color: '#85F49D',
                    width: 2
                  }
                }
              },
              data: this.oil3
            }]
          })
        }
      }).catch(() => {
        this.$message.error('获取油料消耗历史信息失败')
      })
      
    }
  }
}
</script>
