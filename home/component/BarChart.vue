<template>
  <div :class="className" :style="{height:height,width:width}" />
</template>

<script>
import echarts from "echarts";
require("echarts/theme/macarons"); // echarts theme
import { debounce } from "@/utils";
import home from "@/api/home";

const animationDuration = 6000;

export default {
  props: {
    className: {
      type: String,
      default: "chart"
    },
    width: {
      type: String,
      default: "100%"
    },
    height: {
      type: String,
      default: "180px"
    },
    time: {
      type: String
    }
  },
  data() {
    return {
      chart: null,
      data: [],
      showdata: [null, null, null],
      queryParam: {
        pageSize: 10,
        pageNum: 1,
        intervalBeginTime: null,
        intervalEndTime: null
      }
    };
  },
  mounted() {
    this.initChart();
    this.__resizeHandler = debounce(() => {
      if (this.chart) {
        this.chart.resize();
      }
    }, 100);
    window.addEventListener("resize", this.__resizeHandler);
  },
  beforeDestroy() {
    if (!this.chart) {
      return;
    }
    window.removeEventListener("resize", this.__resizeHandler);
    this.chart.dispose();
    this.chart = null;
  },
  watch: {
    time(newVal, oldVal) {
      if (newVal) {
        this.queryParam.intervalBeginTime = newVal;
        this.queryParam.intervalEndTime = newVal;
        this.initChart();
      }
    }
  },
  methods: {
    initChart() {
      home
        .consume(this.queryParam)
        .then(r => {
          console.log(this.time);

          if (r.code === 20000) {
            this.showdata = [];
            this.data = [];
            this.data.push(r.data.油耗详情);
            console.log(this.data);
            this.data[0].forEach((item, index) => {
              if (item.oilType === 0) {
                this.showdata[0] = item.weighter.toFixed(3);
                console.log(item.weighter);
              } else if (item.oilType === 1) {
                this.showdata[1] = item.weighter.toFixed(3);
              } else if (item.oilType === 2) {
                this.showdata[2] = item.weighter.toFixed(3);
              }
            });
            console.log(this.showdata);
            this.chart = echarts.init(this.$el, "macarons");

            this.chart.setOption({
              color: ["#5ec1e5"],
              tooltip: {
                trigger: "axis",
                axisPointer: {
                  // 坐标轴指示器，坐标轴触发有效
                  type: "shadow" // 默认为直线，可选为：'line' | 'shadow'
                }
              },
              grid: {
                top: "25",
                bottom: "1%",
                left: 0,
                right: 0,
                containLabel: true
              },
              xAxis: [
                {
                  type: "category",
                  data: ["汽油", "柴油", "3号喷气燃料油"],
                  axisTick: {
                    show: false,
                    alignWithLabel: true
                  },
                  axisLine: {
                    lineStyle: {
                      type: "solid",
                      color: "#4f7399",
                      width: "2"
                    }
                  },
                  axisLabel: {
                    show: true,
                    textStyle: {
                      color: "#ffffff", //更改坐标轴文字颜色
                      fontSize: 12 //更改坐标轴文字大小
                    },
                    margin: 15
                  }
                }
              ],
              yAxis: [
                {
                  type: "value",
                  axisTick: {
                    show: false
                  },
                  splitLine: {
                    show: true,
                    lineStyle: {
                      type: "solid",
                      color: "#4f7399"
                    }
                  },
                  splitArea: { show: false }, //保留网格区域
                  axisLine: {
                    lineStyle: {
                      type: "solid",
                      color: "#4f7399",
                      width: "2"
                    }
                  },
                  axisLabel: {
                    color: "#ffffff",
                    margin: 15
                  }
                }
              ],
              series: [
                {
                  name: "消耗量",
                  type: "bar",
                  barWidth: "20%",
                  data: this.showdata
                }
              ]
            });
          }
        })
        .catch(() => {
          this.$message.error("获取当前主油消耗量信息失败");
        });
    }
  }
};
</script>
