<template>
  <div class="app-container">
    <div class="filter-container">
       <el-date-picker
            v-model="time"
            type="daterange"
            align="right"
            unlink-panels
            range-separator="至"
            start-placeholder="开始日期"
            end-placeholder="结束日期"
            value-format="yyyy-MM-dd"
            :picker-options="pickerOptions"
            style="width:400px;"
            @change="handleFilter">
        </el-date-picker>
    </div>

    <h5><span class="day">{{ showTime }}</span>设备保养维修次数统计</h5>
    <barchart :times="time"></barchart>
  </div>
</template>

<script>
import Pagination from '@/components/Pagination'
import { mapGetters } from 'vuex'
import online from '@/api/onlineEquip'
import { parseTime } from '@/utils'
import barchart from './components/barchart'

export default {
  components: { Pagination , barchart},
  data() {
    return {
        pickerOptions: {
          shortcuts: [{
            text: '最近一周',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近一个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit('pick', [start, end]);
            }
          }, {
            text: '最近三个月',
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit('pick', [start, end]);
            }
          }]
        },
      // 加载列表
      // 表格
      list: [],
      listLoading: true,
      listTotal: 0,
      // 查询
      listQuery: {
        // pageSize: 10,
        // pageNum: 1,
        intervalBeginTime:null,
        intervalEndTime:null
      },
      time:null,
      showTime:null
    }
  },
  filters:{
  },
  created(){
  },
  computed: {
    ...mapGetters([
      'id'
    ])
  },
  mounted() {
    this.getTime()
  },
  methods: {
    getTime(){
      let date = new Date()
      this.showTime = date.getFullYear()+'年'+((date.getMonth()+1).toString().length<2?'0'+(date.getMonth()+1):(date.getMonth()+1))+'月'
    },
    // 查询列表
    handleFilter() {
      if(this.time){
        this.listQuery.intervalBeginTime = this.time[0]
        this.listQuery.intervalEndTime = this.time[1]
        if(this.listQuery.intervalBeginTime===this.listQuery.intervalEndTime){
          this.showTime = parseTime(this.listQuery.intervalBeginTime, '{y}年{m}月{d}日')
        }else{
          this.showTime = parseTime(this.listQuery.intervalBeginTime, '{y}年{m}月{d}日') + '至' + parseTime(this.listQuery.intervalEndTime, '{y}年{m}月{d}日')
        }
      }else{
        this.listQuery.intervalBeginTime = null
        this.listQuery.intervalEndTime = null
        this.getTime()
      }
    }
  }
}
</script>

<style scoped>
.filter-container {
  margin-bottom:30px;
}
.day {
  color:#ff4949;
}
</style>
