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
            @change="clearTime">
        </el-date-picker>
        <el-button type="primary" icon="el-icon-search" @click="handleFilter">
          查询
        </el-button>
    </div>

    <h5><span class="day">{{ showTime }}</span>油料消耗数据</h5>
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="加载"
      border
      fit
      highlight-current-row
    >
      <el-table-column label="油料" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilType | showOil }}
        </template>
      </el-table-column> 
      <el-table-column label="消耗量" align="center">
        <template slot-scope="scope">
          {{ scope.row.weighter }}
        </template>
      </el-table-column>  
    </el-table>

  </div>
</template>

<script>
import Pagination from '@/components/Pagination'
import { mapGetters } from 'vuex'
import table from '@/api/tableManagement'
import { parseTime } from '@/utils'

export default {
  components: { Pagination },
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
        pageSize: 10,
        pageNum: 1,
        intervalBeginTime:null,
        intervalEndTime:null
      },
      time:null,
      showTime:null
    }
  },
  filters:{
    showType(val){
      switch(parseInt(val)){
        case 0 :
          return '卸油'
        case 1 :
          return '退油'
        case 2 :
          return '飞机加油'
        case 3 :
          return '槽车加油'
        case 4 :
          return '零发油'
      }
    },
    showOil(val){
      switch(parseInt(val)){
        case 0 :
          return '汽油'
        case 1 :
          return '柴油'
        case 2 :
          return '航空3号喷气燃料油'
      }
    }
  },
  created(){
  },
  computed: {
    ...mapGetters([
      'id'
    ])
  },
  mounted() {
    this.getList()
    this.showTime = this.getTime()
  },
  methods: {
    getTime(){
      let date = new Date()
      return date.getFullYear()+'年'+((date.getMonth()+1).toString().length<2?'0'+(date.getMonth()+1):(date.getMonth()+1))+'月'+(date.getDate().toString().length<2?'0'+date.getDate():date.getDate())+'日'
    },
    // 加载列表
    getList() {
      this.listLoading = true
      table.oilConsume(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.list = r.data.油耗详情
          this.listTotal = r.data.total
          this.listLoading = false
        }
      }).catch(() => {
        this.listLoading = false
      })
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
        this.showTime = this.getTime()
      }
      this.listQuery.pageNum = 1
      this.getList()
    },
    clearTime(){
      if(!this.time){
        this.listQuery.intervalBeginTime = null
        this.listQuery.intervalEndTime = null
        this.showTime = this.getTime()
        this.listQuery.pageNum = 1
        this.getList()
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
