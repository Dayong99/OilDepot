<template>
  <div class="app-container">
    <!-- <div class="filter-container">
      <el-select 
      placeholder="油品名称" 
      v-model="listQuery.oilId" 
      @clear="handleFilter" 
      clearable>
        <el-option
          v-for="item in options"
          :key="item.id"
          :label="item.oilName"
          :value="item.id"
        />
      </el-select>
      <el-input 
      v-model="listQuery.batch" 
      placeholder="油品批号" 
      style="width: 200px;" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-button type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
    </div> -->

    <el-table
      v-loading="listLoading"
      :data="list"
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
      <el-table-column v-if="show" label="油品ID">
        <template slot-scope="scope">
          <span>
            {{ scope.row.id }}
          </span>
        </template>
      </el-table-column>
      <el-table-column label="油品名称" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilName }}
        </template>
      </el-table-column>
      <el-table-column label="油品批号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.batch }}</span>
        </template>
      </el-table-column>
      <el-table-column label="规格" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilStandard }}
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

    <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.page" :limit.sync="listQuery.limit" @pagination="getList" />

    <el-dialog :visible.sync="recordVisible" title="收发记录" width="822px" top="5vh">
      <h2 style="text-align:center;">分队油料帐</h2>
      <div class="info">
        <dl>
          <dt>油料名称：</dt>
          <dd>{{name}}</dd> 
        </dl>
        <dl style="float:right;width:90px;">
          <dt>单位：</dt>
          <dd style="width:40px;">公斤</dd>
        </dl>
      </div>

      <el-table
        :data="tableData"
        style="width: 100%"
        border
        fit
        highlight-current-row
      >
        <el-table-column :label="year" align="center">
          <template slot-scope="scope">
              {{ scope.row.createTime?scope.row.createTime.substring(0,4):'' }}
          </template>
          <el-table-column label="月" width="60" align="center">
            <template slot-scope="scope">
              {{ scope.row.createTime?scope.row.createTime.substring(5,7):'' }}
            </template>
          </el-table-column>
          <el-table-column label="日" width="60" align="center">
            <template slot-scope="scope">
              {{ scope.row.createTime?scope.row.createTime.substring(8,10):'' }}
            </template>
          </el-table-column>        
        </el-table-column>
        <el-table-column label="凭证号码" align="center">
          <template slot-scope="scope">
            {{ scope.row.proof }}
          </template>
        </el-table-column>           
        <el-table-column label="单位" align="center">
          <template slot-scope="scope">
            {{ scope.row.unit }}
          </template>
        </el-table-column>               
        <el-table-column label="收入/支出" align="center">
          <template slot-scope="scope">
            {{ scope.row.stype===0?'收入':'支出' }}
          </template>
        </el-table-column>    
        <el-table-column label="数量" align="center">
          <template slot-scope="scope">
            {{ scope.row.amount }}
          </template>
        </el-table-column>                                 
        <el-table-column label="结存" align="center">
          <template slot-scope="scope">
            {{ scope.row.jiecun }}
          </template>
        </el-table-column>
      </el-table>
      <el-pagination
        @current-change="getPage"
       :current-page="page"
       layout="total, prev, pager, next"
       :page-count="count">
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
      // 加载油品列表
      options: [],
      // 表格
      list: [],
      listLoading: true,
      listTotal: 0,
      // 查询
      listQuery: {
        page: 1, // 初始页
        limit: 10, //    每页的数据
        oilId:null,
        batch:null
      },

      // 收发记录

      recordVisible: false,
      tableData: [],
      year:null,
      name:null,
      oilId:null,
      count:0,
      page:1
    }
  },
  created() {
    this.getList()
    this.getOilList()
    this.showCol()
  },
  methods: {
    // 隐藏某列
    showCol() {
      this.show = false
    },
    // 获取油品列表
    getOilList() {
      getOil(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.options = r.data.rows
        }
      }).catch(() => {
        setTimeout(() => {
          this.$message.error('获取油品信息失败！')
        }, 500)
      })
    },
    // 加载列表
    getList() {
      this.listLoading = false
      oilNumber(this.listQuery).then((r) => {
          if (r.code === 20000) {
            this.list = r.data.pageInfo
            this.listTotal = r.data.total
            this.listLoading = false
          }
        }).catch(() => {
          this.listLoading = false
        })
    },
    // // 查询列表
    // handleFilter() {
    //   this.listQuery.page = 1
    //   this.getList()
    // },
    // 收发记录
    showRecord(row) {
      this.name = row.oilName
      this.oilId= row.oilId
      console.log(this.oilId)
      this.recordVisible = true
      this.getRecord()
    },
    getRecord(){
      recordDetail({
        receiptOilId:this.oilId,
  	    page:this.page
      }).then((r) => {
          if (r.code === 20000) {
            this.tableData = r.data.Info
            this.year = this.tableData[0].createTime?(this.tableData[0].createTime.substring(0,4) + ' 年'):''
            this.count = r.data.count
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
    width:300px;
    margin:10px 0;
}
.info dl:first-child {
  margin-left:13px;
}

.info dt {
    float:left;
}
.info dd {
    width:200px;
    height:30px;
    display:block;
    float: left;
    text-align:left;
    margin-left:0;
}
</style>
