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
      <el-input 
      v-model="listQuery.airplane" 
      placeholder="飞机名称" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-input 
      v-model="listQuery.airplaneDept" 
      placeholder="单位" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-input 
      v-model="listQuery.recorder" 
      placeholder="机务" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
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
      <el-table-column label="飞机号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.fighterNum }}</span>
        </template>
      </el-table-column>
      <el-table-column label="实发升数" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilVolume }}
        </template>
      </el-table-column>
      <el-table-column label="重量" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilWeight }}
        </template>
      </el-table-column>
      <el-table-column label="密度" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilDensity }}
        </template>
      </el-table-column>  
      <el-table-column label="保管员" align="center">
        <template slot-scope="scope">
          {{ scope.row.receiverId }}
        </template>
      </el-table-column>  
      <el-table-column label="机务" align="center">
        <template slot-scope="scope">
          {{ scope.row.recorderId }}
        </template>
      </el-table-column>
      <el-table-column label="开小票编号" align="center">
        <template slot-scope="scope">
          {{ scope.row.kxpNumber }}
        </template>
      </el-table-column>
      <el-table-column label="表前数" align="center">
        <template slot-scope="scope">
          {{ scope.row.frontTable }}
        </template>
      </el-table-column>
      <el-table-column label="表后数" align="center">
        <template slot-scope="scope">
          {{ scope.row.afterTable }}
        </template>
      </el-table-column>
      <el-table-column label="加油单元" align="center">
        <template slot-scope="scope">
          {{ scope.row.tableNumber }}
        </template>
      </el-table-column>
      <el-table-column label="单位" align="center">
        <template slot-scope="scope">
          {{ scope.row.airplaneDept }}
        </template>
      </el-table-column>
      <!-- <el-table-column label="车/飞机号" align="center"> -->
      <el-table-column label="飞机名称" align="center">
        <template slot-scope="scope">
          {{ scope.row.airplane }}
        </template>
      </el-table-column>
      <el-table-column label="折合升数" align="center">
        <template slot-scope="scope">
          {{ scope.row.convertedRise }}
        </template>
      </el-table-column>
      <el-table-column label="机型" align="center">
        <template slot-scope="scope">
          {{ scope.row.airmodel }}
        </template>
      </el-table-column>
      <el-table-column label="确认状态" align="center">
        <template slot-scope="scope">
          <el-tag :type="scope.row.issueStatus===0?'info':'success'">{{ scope.row.issueStatus | showType }}</el-tag>
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center" width="100" class-name="small-padding fixed-width">
        <template slot-scope="{row}">
          <el-button type="primary" size="mini" @click="oilFuel(row)">
            确认
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="getList" />

    <el-dialog :visible.sync="dialogOilVisible" title="确认" @close="cancleForm" top="30vh" width="920px">
      <el-form :inline="true" ref="billForm" label-position="right" :rules="billRules" :model="billData" label-width="110px" size="small">
            <el-form-item label="飞机号" prop="aircraftId">
                <el-select
                    clearable
                    placeholder="请选择"
                    v-model="billData.aircraftId"
                    >
                    <el-option
                        v-for="item in planeOption"
                        :key="item.id"
                        :label="item.fighterNum"
                        :value="item.id"
                    />
                </el-select>
             </el-form-item>
             <el-form-item label="密度" prop="oilDensity" >
                <el-input-number v-model="billData.oilDensity" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
             <el-form-item label="实发升数" prop="oilVolume">
                <el-input-number v-model="billData.oilVolume" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitForm()">提交</el-button>
        <el-button @click="cancleForm">取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import Pagination from '@/components/Pagination'
import { mapGetters } from 'vuex'
import fuel from '@/api/fuelManagement'

export default {
  components: { Pagination },
  data() {
    return {
      // 查询
      pickerOptions1: {
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
        pickerOptions: {
          shortcuts: [{
            text: '今天',
            onClick(picker) {
              picker.$emit('pick', new Date());
            }
          }, {
            text: '昨天',
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24);
              picker.$emit('pick', date);
            }
          }, {
            text: '一周前',
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit('pick', date);
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
        intervalEndTime:null,
        airplaneDept:null,
        airplane:null,
        recorder:null
      },
      time:null,

      // 新增弹窗
      dialogOilVisible: false,

      billData: {
        // id:null,
        // aircraftId:null,
        // oilVolume:null,
        // oilDensity:null

        afterTable: null,
        airmodel: null,
        airplane: null,
        airplaneDept: null,
        convertedRise: null,
        createTime: null,
        fighterNum: null,
        frontTable: null,
        id: null,
        issueStatus: null,
        kxpNumber: null,
        oilDensity:null,
        oilVolume: null,
        oilWeight: null,
        receiverId: null,
        recorderId: null
      },
      billRules: {
        aircraftId: [
          { required: true, message: '请选择飞机号', trigger: 'change' }
        ],
        oilVolume: [
          { required: true, message: '密度不能为空', trigger: 'change' }
        ],
        oilDensity: [
          { required: true, message: '实发升数不能为空', trigger: 'change' }
        ]
      },
      planeOption:[]
    }
  },
   filters:{
    showType(val){
      switch(parseInt(val)){
        case 0 :
          return '未确认'
        case 1 :
          return '已确认'
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
    this.getPlane().then(data => {
      this.planeOption = data
    })
    this.getList()
  },
  methods: {
    // 加载列表
    getList() {
      this.listLoading = true
      fuel.planeFuelList(this.listQuery).then((r) => {
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
      if(this.time){
        this.listQuery.intervalBeginTime = this.time[0]
        this.listQuery.intervalEndTime = this.time[1]
      }else{
        this.listQuery.intervalBeginTime = null
        this.listQuery.intervalEndTime = null
      }
      this.listQuery.pageNum = 1
      this.getList()
    },
    clearTime(){
      if(!this.time){
        this.listQuery.intervalBeginTime = null
        this.listQuery.intervalEndTime = null
        this.listQuery.pageNum = 1
        this.getList()
      }
    },

    // 重置新增列表
    resetFormList() {
      this.billData = {
        afterTable: null,
        airmodel: null,
        airplane: null,
        airplaneDept: null,
        convertedRise: null,
        createTime: null,
        fighterNum: null,
        frontTable: null,
        id: null,
        issueStatus: null,
        kxpNumber: null,
        oilDensity:null,
        oilVolume: null,
        oilWeight: null,
        receiverId: null,
        recorderId: null
      }
      this.$refs['billForm'].resetFields()
    },
    // 打开新增弹窗
    oilFuel(row) {
      this.billData = Object.assign({}, row) 
      for(let i = 0;i<this.planeOption.length;i++){
          if(parseInt(row.fighterNum)===parseInt(this.planeOption[i].fighterNum)){
            this.billData.aircraftId =  this.planeOption[i].id
          }
      }
      this.dialogOilVisible = true
    },
   
    // 提交表单
    submitForm() {
      this.$refs['billForm'].validate((valid) => {
        if (valid) {
          fuel.planeFuel(this.billData).then((r) => {
            if (r.code === 20000) {
              this.dialogOilVisible = false
              this.$message({
                message: '发油成功',
                type: 'success'
              })
              this.resetFormList()
              this.getList()
            }
          }).catch(() => {
            setTimeout(() => {
              this.$message({
                message: '发油失败',
                type: 'error'
              })
            }, 500)
          })
        } else { // 验证不通过
         this.$message({
            message: '请填写完整信息',
            type: 'warning'
          })
        }
      })
    },
    // 取消
    cancleForm() {
      this.dialogOilVisible = false
      this.resetFormList()
    }
  }
}
</script>

<style scoped>
.filter-container {
  margin-bottom:30px;
}
.el-select {
    width:150px;
}
.el-input {
    width:150px;
}
.el-input-number {
    width:150px;
}
.el-form-item i{
    color:#909399;
    font-size:16px;

}
</style>
