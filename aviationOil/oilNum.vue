<template>
  <div class="app-container">
    <div class="filter-container">
       <el-select
        v-model="listQuery.oilType"
        placeholder="油品"
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
      <el-table-column label="更新时间" align="center">
        <template slot-scope="scope">
          {{ scope.row.updateTime }}
        </template>
      </el-table-column>
      <el-table-column label="油品名称" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilType | showType }}
        </template>
      </el-table-column>
      <el-table-column label="库存" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilStock }}
        </template>
      </el-table-column>  
      <el-table-column label="体积 (L)" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilVolume }}
        </template>
      </el-table-column>  
    </el-table>

    <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="getList" />
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
      // 加载列表
      // 表格
      list: [],
      listLoading: true,
      listTotal: 0,
      // 查询
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        oilType:null
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
      table.oilNum(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.list = r.data.rows
          this.listTotal = r.data.total
          this.listLoading = false
        }
      }).catch(() => {
        this.listLoading = false
      })
    },
    // 查询列表
    handleFilter() {
      this.listQuery.pageNum = 1
      this.getList()
    }
  }
}
</script>

<style scoped>
.filter-container {
  margin-bottom:30px;
}
</style>
