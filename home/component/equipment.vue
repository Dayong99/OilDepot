<template>
  <div class="app-container">
    <!-- <div class="filter-container">
      <el-select 
      v-model="listQuery.equipid" 
      placeholder="器材名称"
      @clear="handleFilter"  
      clearable >
        <el-option
          v-for="eitem in equipOptions"
          :key="eitem.id"
          :label="showName(eitem)"
          :value="eitem.id"
        />
      </el-select>
      <el-select 
      v-model="listQuery.equiptype" 
      placeholder="类型" 
      @clear="handleFilter" 
      clearable>
        <el-option
          v-for="titem in typeOptions"
          :key="titem.value"
          :label="titem.label"
          :value="titem.value"
        />
      </el-select>
      <el-select 
      v-model="listQuery.equipareaid" 
      placeholder="位置" 
      @clear="handleFilter" 
      clearable>
        <el-option
          v-for="aitem in areaOptions"
          :key="aitem.index"
          :label="aitem.areaName"
          :value="aitem.id"
        />
      </el-select>
      <el-button type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
    </div> -->

    <el-table
      v-loading="listLoading"
      :data="equipList"
      element-loading-text="Loading"
      border
      fit
      highlight-current-row
    >
      <el-table-column align="center" label="ID" width="50">
        <template slot-scope="scope">
          {{ scope.$index+1 }}
        </template>
      </el-table-column>
      <el-table-column label="器材名称" align="center">
        <template slot-scope="scope">
          {{ scope.row.equipName }}
        </template>
      </el-table-column>
      <el-table-column label="型号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.equipStandard }}</span>
        </template>
      </el-table-column>
      <el-table-column label="类型" align="center">
        <template slot-scope="scope">
          {{ scope.row.type | showType }}
        </template>
      </el-table-column>
      <el-table-column label="位置" align="center">
        <template slot-scope="scope">
          {{ scope.row.areaName }}
        </template>
      </el-table-column>
      <el-table-column label="当前数量" align="center">
        <template slot-scope="scope">
          {{ scope.row.amount }}
        </template>
      </el-table-column>
      <el-table-column class-name="status-col" label="收发记录" width="150" align="center">
        <template slot-scope="{row}">
          <el-button type="primary" plain size="mini" @click="showRecord(row)">查看</el-button>
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.page" :limit.sync="listQuery.limit" @pagination="getEquipList" />

    <el-dialog :visible.sync="recordVisible" title="收发记录" width="762px" top="5vh">
      <h2 style="text-align:center;">油料装备帐</h2>
      <div class="info">
        <dl style="float:right;width:90px;">
          <dt>单位：</dt>
          <dd style="width:40px;">元</dd>
        </dl>
      </div>

      <el-table
        :data="tableData"
        style="width: 100%"
      >
        <el-table-column :label="year" align="center">
          <template slot-scope="scope">
            {{ scope.row.operatetime.substring(0,4) }}
          </template>
          <el-table-column label="月" width="60" align="center">
            <template slot-scope="scope">
              {{ scope.row.operatetime.substring(5,7) }}
            </template>
          </el-table-column>
          <el-table-column label="日" width="60" align="center">
            <template slot-scope="scope">
              {{ scope.row.operatetime.substring(8,10) }}
            </template>
          </el-table-column>        
        </el-table-column>
        <el-table-column label="凭证号码" width="100" align="center">
          <template slot-scope="scope">
            {{ scope.row.proof }}
          </template>
        </el-table-column>           
        <el-table-column label="单位" width="100" align="center">
          <template slot-scope="scope">
            {{ scope.row.unit }}
          </template>
        </el-table-column>               
        <el-table-column label="收入/支出" width="100" align="center">
          <template slot-scope="scope">
            {{ scope.row.stype===0?'收入':'支出' }}
          </template>
        </el-table-column>    
        <el-table-column label="数量" width="100" align="center">
          <template slot-scope="scope">
            {{ scope.row.amount }}
          </template>
        </el-table-column>                   
        <el-table-column label="类型" width="100" align="center">
          <template slot-scope="scope">
            {{ scope.row.etype | showType }}
          </template>
        </el-table-column>                
        <el-table-column label="结存" width="100" align="center">
          <template slot-scope="scope">
            {{ scope.row.jiecun }}
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        @current-change="getPage"
       :current-page="page"
       layout="total, prev, pager, next"
       :page-count="total">
      </el-pagination>    
    </el-dialog>
  </div>
</template>

<script>
import Pagination from '@/components/Pagination'
import operation from '@/api/internalOperation'

export default {
  components: { Pagination },
  data() {
    return {
      equipOptions:[],
      areaOptions:[],
      typeOptions: [
        {
          label: '新品',
          value: 0
        },
        {
          label: '堪用品',
          value: 1
        },
        {
          label: '战备',
          value: 2
        }
      ],
      // 表格
      equipList:[],
      listLoading: true,
      listTotal:0,
      // 查询
      listQuery: {
        limit: 10, //    每页的数据
        page: 1,
        equipid:null,
        equiptype:null,
        equipareaid:null
      },

      // 收发记录
      recordVisible: false,
      tableData: [],
      year:'',
      id:null,
      type:null,
      page:1,
      total:0

    }
  },
  filters:{
    showType(val) {
      switch (parseInt(val)) {
        case 0 :
          return '新品'
        case 1 :
          return '堪用品'
        case 2 :
          return '战备'
      }
    }
  },
  created() {
    this.getEquipList()
    this.getEquip()
    this.getArea()
  },
  methods: {
    getArea() {
      getArea().then((r) => {
        if (r.code === 20000) {
          this.areaOptions = r.data
        }
      }).catch(() => {
        setTimeout(() => {
          this.$message.error('获取区域信息失败！')
        }, 500)
      })
    },
    // 获取器材列表
    getEquip() {
      getEquipList().then((r) => {
        if (r.code === 20000) {
          this.equipOptions = r.data
        }
      }).catch(() => {
        setTimeout(() => {
          this.$message.error('获取器材信息失败！')
        }, 500)
      })
    },
    // 显示型号
    showName(val) {
      // return val.equipName + '（' + val.equipStandard + '）'
      return val.equipName + '（' + val.equipStandard + '）'
    },
     // 库存
    getEquipList() {
      this.listLoading = true
      equipNumber(this.listQuery).then((r) => {
          if (r.code === 20000) {
            this.equipList = r.data.rows
            this.listTotal = r.data.totals
            this.listLoading = false
          }
        }).catch(() => {
          this.listLoading = false
        })
    },
    // handleFilter(){
    //   this.listQuery.page = 1
    //   this.getEquipList()
    // },
    // 收发记录
    showRecord(row) {
      this.id = row.id
      this.type= row.type
      this.recordVisible = true
      this.getRecord()
    },
    getRecord(){
      getRecord({
        equipid:this.id,
  	    equiptype:this.type,
  	    page:1
      }).then((r) => {
          if (r.code === 20000) {
            this.tableData = r.data.Info
            this.year = this.tableData[0].operatetime.substring(0,4) + ' 年'
            this.total = r.data.count
          }
        }).catch(() => {
      })
    },
    getPage(val){
      this.page = val
      this.getRecord()
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
.info {
    margin-bottom:30px;
}

.info dl {
    float:left;
    line-height:30px;
    width:200px;
    margin:10px 0;
}
.info dl:first-child {
  margin-left:13px;
}

.info dt {
    float:left;
}
.info dd {
    width:90px;
    height:30px;
    display:block;
    float: left;
    text-align:left;
    margin-left:0;
}
</style>
