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

    <h5><span class="day">{{ month }}</span>进销存计量统计报表</h5>
    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="加载"
      border
      fit
      highlight-current-row
    >
      <el-table-column align="center" label="ID" width="50">
        <template slot-scope="scope">
          {{ scope.$index+1 }}
        </template>
      </el-table-column>
      <el-table-column label="油品" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilType | showOil }}
        </template>
      </el-table-column> 
      <el-table-column label="业务类型" align="center">
        <template slot-scope="scope">
          {{ scope.row.fransferType | showType }}
        </template>
      </el-table-column>
      <el-table-column label="数量" align="center">
        <template slot-scope="scope">
          {{ scope.row.total.toFixed(3) }}
        </template>
      </el-table-column>  
    </el-table>

    <!-- <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="getList" /> -->
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
        oilOption:[
        {
          label:'汽油',
          value:0
        },
        {
          label:'柴油',
          value:1
        },
        {
          label:'3号喷气燃料',
          value:2
        }
      ],
      typeOption:[
        {
          label:'卸油',
          value:0
        },
        {
          label:'退油',
          value:1
        },
        {
          label:'飞机加油',
          value:2
        },
        {
          label:'槽车加油',
          value:3
        },
        {
          label:'零发油',
          value:4
        }
      ],
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
        intervalBeginTime:null,
        intervalEndTime:null
      },
      time:null,
      month:null
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
    this.month = this.getTime()
  },
  methods: {
    getTime(){
        let date = new Date()
        return date.getFullYear() + '年'+(date.getMonth()+1)+'月'+date.getDate() + '日'
    },
    // 加载列表
    getList() {
      this.listLoading = true
      table.invoicingTable(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.list = r.data
        //   this.listTotal = r.data.total
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
          this.month = parseTime(this.listQuery.intervalBeginTime,'{y}年{m}月{d}日')
        }else{
          this.month = parseTime(this.listQuery.intervalBeginTime,'{y}年{m}月{d}日') + '至' + parseTime(this.listQuery.intervalEndTime,'{y}年{m}月{d}日')
        }
      }else{
        this.listQuery.intervalBeginTime = null
        this.listQuery.intervalEndTime = null
        this.month = this.getTime()
      }
    //   this.listQuery.pageNum = 1
      this.getList()
    },
    clearTime(){
     if(!this.time){
        this.listQuery.intervalBeginTime = null
        this.listQuery.intervalEndTime = null
        this.month = this.getTime()
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
