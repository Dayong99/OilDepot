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
      default: '100px'
    }
  },
  data() {
    return {
      chart: null,
      useRate:0
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
      home.repertory().then((r) => {
        if (r.code === 20000) {
          if(r.data.Diesel){
          this.useRate = (r.data.Diesel.useRate*100).toFixed(2)
            //const data = r.data.Diesel
          }
          this.chart = echarts.init(this.$el, 'macarons')

            this.chart.setOption({
              title:{
                    show:true,
                    text:this.useRate+'%',
                    x:'center',
                    y:'center',
                    textStyle: {
                            fontSize: '16',
                            color:'white',
                            fontWeight: 'normal'
                        }
                },
                grid:{
                  bottom:0
                },
              tooltip: {
                  trigger: 'item',
                  formatter: "使用率<br/>柴油: "+this.useRate+"%"
              },
              // tooltip: {
              //     trigger: 'item',
              //     formatter: function(value){
              //       let val = data.sum-data.allowance
              //       return "使用率<br/>柴油: "+ val.toFixed(2)
              //     }
              // },
              legend: {
                  orient: 'vertical',
                  x: 'left',
                  show:false
              },
              series: 
                  {
                      name:'',
                      type:'pie',
                      radius: ['75%', '85%'],
                      avoidLabelOverlap: true,
                      hoverAnimation:false,
                      label: {
                          normal: {
                              show: false,
                              position: 'center'
                          },
                          emphasis: {
                              show: false
                          }
                      },
                      labelLine: {
                          normal: {
                              show: false
                          }
                      },
                      data:[
                          {value:this.useRate, name:'柴油'},
                          {value:100-this.useRate, name:''}
                      ]
                      },
                      color:['#f8b00a','#3c404c']
            })
        }
      }).catch(() => {
        this.$message.error('获取当前库存信息失败')
      })
      
    }
  }
}
</script>
