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
       <el-select
        v-model="listQuery.oilType"
        placeholder="发出油品"
        style="width:200px;"
        clearable
        @clear="handleFilter"
      >
        <el-option
          v-for="item in typeOption"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        />
      </el-select>
      <el-select
        v-model="listQuery.fransferType"
        placeholder="业务类型"
        style="width:200px;"
        clearable
        @clear="handleFilter"
      >
        <el-option
          v-for="item in businessType"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        />
      </el-select>
      <el-button type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
    </div>

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
      <el-table-column label="日期" align="center" width="200">
        <template slot-scope="scope">
          {{ scope.row.createTime }}
        </template>
      </el-table-column>
      <el-table-column label="发出油品" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilType | showType}}
        </template>
      </el-table-column>  
      <el-table-column label="业务" align="center">
        <template slot-scope="scope">
          {{ scope.row.fransferType | showBusiness }}
        </template>
      </el-table-column>  
      <el-table-column label="体积 (L)" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilVolume }}
        </template>
      </el-table-column>
      <el-table-column label="重量 (kg)" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilWeight }}
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.page" :limit.sync="listQuery.pageSize" @pagination="getList" />
  </div>
</template>

<script>
import Pagination from '@/components/Pagination'
import { mapGetters } from 'vuex'
import table from '@/api/tableManagement'

export default {
  components: { Pagination },
  data() {
    return {
      typeOption:[
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
      businessType:[
        {
          label:'铁路卸油',
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
        },
        {
          label:'地面加油',
          value:5
        }
      ],
      // 查询
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
        page: 1,
        beginTime:null,
        endTime: null,
        fransferType: null,
        oilType:null,
        tableName:'oilflow'
      },
      time:null
    }
  },
  filters:{
    showType(val){
      switch(parseInt(val)){
        case 0 :
          return '汽油'
        case 1 :
          return '柴油'
        case 2 :
          return '航空3号喷气燃料油'
      }
    },
    showBusiness(val){
      switch(parseInt(val)){
        case 0 :
          return '铁路卸油'
        case 1 :
          return '退油'
        case 2 :
          return '飞机加油'
        case 3 :
          return '槽车加油'
        case 4 :
          return '零发油'
        case 5 :
          return '地面加油'
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
  },
  methods: {
    // 加载列表
    getList() {
      this.listLoading = true
      table.fuelingRecord(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.list = r.data.scores
          this.listTotal = r.data.scoresCount
          this.listLoading = false
        }
      }).catch(() => {
        this.listLoading = false
      })
    },
    // 查询列表
    handleFilter() {
      if(this.time){
        this.listQuery.beginTime = this.time[0]
        this.listQuery.endTime = this.time[1]
      }else{
        this.listQuery.beginTime = null
        this.listQuery.endTime = null
      }
      this.listQuery.page = 1
      this.getList()
    },
    clearTime(){
      if(!this.time){
        this.listQuery.beginTime = null
        this.listQuery.endTime = null
        this.listQuery.page = 1
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
</style>
