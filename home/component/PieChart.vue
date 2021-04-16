<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import home from '@/api/home'
import echarts from 'echarts'
require('echarts/theme/macarons') // echarts theme
import { debounce } from '@/utils'
var e = 80
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
      default: '180px'
    }
  },
  data() {
    return {
      chart: null,
      oil1:null,
      oil2:null,
      oil3:null
    }
  },
  mounted() {
    this.getOil()
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
      home.total().then((r) => {
        if (r.code === 20000) {
          r.data.forEach((item,index)=>{
            if(item.oilType===0){
              this.oil1 = item.oilStock
            }else if(item.oilType===1){
              this.oil2 = item.oilStock
            }else if(item.oilType===2){
              this.oil3 = item.oilStock
            }
          })
            this.chart = echarts.init(this.$el, 'macarons')
            this.chart.setOption({
              tooltip: {
                  trigger: 'item',
                  formatter: "{d}%",
                  show:false
              },
              grid:{
                bottom:'1%',
                left:20,
                right:0,
                containLabel: true
              },
              legend: {
                  show:true,
                  right:0,
                  orient: 'vertical',
                  itemWidth:15,
                  itemHeight:10,
                  itemGap:13,
                  data:['汽油','柴油','航空3号喷气燃料油'],
                  textStyle:{
                    color:'white'
                  }
              },
              tooltip : {
                  trigger: 'item',
                  formatter: "{a} <br/>{b} : {c}"
              },
              series: 
                  [   
                      {
                          name: '库存量',
                          type: 'pie',
                          radius: ['65%', '80%'],
                          avoidLabelOverlap: true,
                          hoverAnimation:false,
                          center: ['150px', '50%'],
                          data:[
                              {value:this.oil1, name:'汽油'},
                              {value:this.oil2, name:'柴油'},
                              {value:this.oil3,name:'航空3号喷气燃料油'}
                          ],
                          label: {
                              normal: {
                                formatter: '{d}%',
                                  textStyle: {
                                      color: '#00d7fe',
                                      fontSize:16
                                  }
                              }
                          },
                          labelLine: {
                              normal: {
                                  lineStyle: {
                                      color: 'rgba(0, 215, 254, 0.4)'
                                  },
                                  smooth: 0.2,
                                  length: 3
                              }
                          },
                          itemStyle: {
                              emphasis: {
                                  shadowBlur: 10,
                                  shadowOffsetX: 0,
                                  shadowColor: 'rgba(128, 128, 128, 0.5)'
                              }
                          }
                      }
                  ],
                  color:['#5448b2','#4998d2','#da3b83']
            })
          console.log(this.oil1,this.oil2,this.oil3)
        }
      }).catch(() => {
        this.$message.error('获取主油库存总量信息失败')
      })
      
    }
  }
}
</script>
