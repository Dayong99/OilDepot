<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.equipName" 
      placeholder="设备名称" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <!-- <el-input 
      v-model="listQuery.equipNum" 
      placeholder="设备编号" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" /> -->
      <el-button class="filter-item" type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
    </div>

    <el-table
      v-loading="listLoading"
      :data="list"
      border
      fit
      highlight-current-row
      style="width: 100%;"
    >
      <el-table-column label="编号" align="center" width="80">
        <template slot-scope="scope">
          <span>{{ scope.row.id }}</span>
        </template>
      </el-table-column>
      <el-table-column label="设备名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.equipName }}</span>
        </template>
      </el-table-column>

      <el-table-column label="设备类型" align="center">
        <template slot-scope="scope">
          <span v-text="showType(scope.row.equipType)"></span>
        </template>
      </el-table-column>
      <el-table-column label="设备编号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.equipNum }}</span>
        </template>
      </el-table-column>
      <el-table-column label="创建日期" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.createtime }}</span>
        </template>
      </el-table-column>
      <el-table-column label="保养周期" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.maintenance }}</span>
        </template>
      </el-table-column>
      <el-table-column label="上次保养日" align="center">
        <template slot-scope="scope">
          <span v-text="showTime(scope.row.maintenanceday)"></span>
        </template>
      </el-table-column>
      <!-- <el-table-column label="在线状态" align="center">
        <template slot-scope="scope">
          <i :class="parseInt(scope.row.isOnline)===0?'el-icon-error noUse':'el-icon-success isUse'" />       
       </template>
      </el-table-column> -->
      <el-table-column label="说明" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.remark }}</span>
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="total>0" :total="total" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="getList" />
  </div>
</template>

<script>
import info from '@/api/baseInfo'
import online from '@/api/onlineEquip'
import Pagination from '@/components/Pagination'
import { parseTime } from '@/utils'

export default {
  components: { Pagination },
  data() {
    return {
      typeOption:[],
      list: null,
      total: 0,

      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        equipName:null
      }
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getMonitorEquipType().then(data => {
      this.typeOption = data
    })
  },
  methods: {
    showTime(val){
        if(val){
            return parseTime(val, '{y}-{m}-{d}') 
        }
    },
    showType(val){
      for(let i = 0;i<this.typeOption.length;i++){
        if(parseInt(val)===parseInt(this.typeOption[i].baseValue)){
          return this.typeOption[i].baseKey
        }
      }
    },
    getList(val) {
      this.listLoading = true
      online.allEquip(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.list = r.data.rows
          this.total = r.data.total
          this.listLoading = false
        }
      }).catch(() => {
        this.listLoading = false
      })
    },
    handleFilter() {
      this.listQuery.pageNum = 1
      this.getList()
    },
    handleModifyStatus(row, status) {
      this.handleDelete(row)
    }
  }
}
</script>

<style scoped>
.filter-container {
  margin-bottom:30px;
}
.table-pagination {
  margin-top:30px;
}
.el-input,.el-select {
  width:200px;
}
.isUse {
    color:#13ce66;
}
.noUse {
    color:#ff4949;
}
i {
    font-size:1.5em;
}
</style>
