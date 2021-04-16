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
        v-model="listQuery.airId"
        placeholder="飞机号"
        clearable
        style="width:200px;"
        @clear="handleFilter"
      >
        <el-option
          v-for="item in planeOption"
          :key="item.id"
          :label="item.fighterNum"
          :value="item.id"
        />
      </el-select>
      <el-button type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
      <el-button type="primary" icon="el-icon-s-order" @click="planeFuel">
        加油
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
          <span v-text="showPlane(scope.row.aircraftId)"></span>
        </template>
      </el-table-column>
      <el-table-column label="机务" align="center">
        <template slot-scope="scope">
          {{ scope.row.receiver }}
        </template>
      </el-table-column>
      <el-table-column label="密度" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilDensity }}
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
      <el-table-column label="经办记录人" align="center">
        <template slot-scope="scope">
          {{ scope.row.recorder }}
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.page" :limit.sync="listQuery.pageSize" @pagination="getList" />

    <el-dialog :visible.sync="dialogOilVisible" title="加油单元加油" width="1090px" @close="cancleForm" top="30vh">
      <el-form :inline="true" ref="billForm" label-position="right" :rules="billRules" :model="billData" label-width="80px" size="small">
        <el-row>
          <el-col :span="24">
            <el-form-item label="日期" prop="createTime">
                <el-date-picker
                v-model="billData.createTime"
                type="datetime"
                value-format="yyyy-MM-dd HH:mm:ss"
                :picker-options="pickerOptions1"
                placeholder="选择日期"
                style="width:190px;">
                </el-date-picker>
            </el-form-item>
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
            <el-form-item label="机务" prop="receiver">
                <el-input v-model="billData.receiver" clearable />
            </el-form-item>
            <el-form-item label="经办记录人" prop="recorder" label-width="110px">
                <el-input v-model="billData.recorder" clearable />
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">    
            <el-form-item label="密度" prop="oilDensity">
                <el-input-number v-model="billData.oilDensity" :min="0" label="描述文字" style="margin-right:40px;"></el-input-number>
            </el-form-item>
            <el-form-item label="体积 (L)" prop="oilVolume" >
                <el-input-number v-model="billData.oilVolume" :min="0" label="描述文字"></el-input-number> 
            </el-form-item>
            <el-form-item label="重量 (kg)" prop="oilWeight">
                <el-input-number v-model="billData.oilWeight" :min="0" label="描述文字"></el-input-number> 
            </el-form-item>
          </el-col>
        </el-row>
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
      planeOption:[],
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
        pickerOptions1: {
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
        page: 1,
        beginTime:null,
        endTime: null,
        airId: null
      },
      time:null,

      // 新增弹窗
      dialogOilVisible: false,

      billData: {
        createTime: null,
        recorder:null,
        aircraftId:null,
        receiver:null,
        oilDensity:null,
        oilVolume:null,
        oilWeight:null
      },
      billRules: {
        createTime: [
          { required: true, message: '日期不能为空', trigger: 'blur' }
        ],
         recorder: [
          { required: true, message: '经办记录人不能为空', trigger: 'blur' }
        ],
        aircraftId: [
          { required: true, message: '请选择飞机号', trigger: 'change' }
        ],
        receiver: [
          { required: true, message: '机务不能为空', trigger: 'blur' }
        ],
        oilDensity: [
          { required: true, message: '密度不能为空', trigger: 'change' }
        ],
        oilVolume: [
          { required: true, message: '体积不能为空', trigger: 'change' }
        ],
        oilWeight: [
          { required: true, message: '重量不能为空', trigger: 'change' }
        ]
      }
    }
  },
  created(){
    this.getPlane().then(data => {
      this.planeOption = data
    })
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
    showPlane(val){
      for (let j = 0; j < this.planeOption.length; j++) {
        if (val === this.planeOption[j].id) {
          return this.planeOption[j].fighterNum
        }
      }
    },
    // 加载列表
    getList() {
      this.listLoading = true
      fuel.planeList(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.list = r.data.scores
          this.listTotal = r.data.socresCount
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
    },

    // 重置新增列表
    resetFormList() {
      this.billData = {
        createTime: null,
        recorder:null,
        aircraftId:null,
        receiver:null,
        oilDensity:null,
        oilVolume:null,
        oilWeight:null
      }
      this.$refs['billForm'].resetFields()
    },
    // 打开新增弹窗
    planeFuel() {
      this.dialogOilVisible = true
    },
   
    // 提交表单
    submitForm() {
      this.$refs['billForm'].validate((valid) => {
        if (valid) {
          this.infoFlag = true
        } else { // 验证不通过
          this.infoFlag = false
          return false
        }
      })

      if (this.infoFlag === false) {
        this.$message({
          message: '请填写完整加油单元加油信息',
          type: 'warning'
        })
      }else{
        fuel.planeFuel(this.billData).then((r) => {
          if (r.code === 20000) {
            this.dialogOilVisible = false
            this.$message({
              message: '加油成功',
              type: 'success'
            })
            this.resetFormList()
            this.getList()
          }
        }).catch(() => {
          setTimeout(() => {
            this.$message({
              message: '加油失败',
              type: 'error'
            })
          }, 500)
        })
      }
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
