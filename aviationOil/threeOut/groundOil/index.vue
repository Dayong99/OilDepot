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
      <el-button type="primary" icon="el-icon-s-order" @click="oilFuel">
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
      <el-table-column label="油品" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilType | showType}}
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
      <el-table-column label="车辆" align="center">
        <template slot-scope="scope">
          {{ scope.row.car }}
        </template>
      </el-table-column>
      <el-table-column label="司机" align="center">
        <template slot-scope="scope">
          {{ scope.row.driver }}
        </template>
      </el-table-column>
      <el-table-column label="经办记录人" align="center">
        <template slot-scope="scope">
          {{ scope.row.recorder }}
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.page" :limit.sync="listQuery.pageSize" @pagination="getList" />

    <el-dialog :visible.sync="dialogOilVisible" title="地面加油" width="838px" @close="cancleForm" top="30vh">
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
             <el-form-item label="油品" prop="oilType">
                <el-select
                    clearable
                    placeholder="请选择"
                    v-model="billData.oilType"
                    >
                    <el-option
                        v-for="item in typeOption"
                        :key="item.value"
                        :label="item.label"
                        :value="item.value"
                    />
                </el-select>
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
            <el-form-item label="重量 (kg)" prop="oilWeight" label-width="110px">
                <el-input-number v-model="billData.oilWeight" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="车辆" prop="car">
                <el-input v-model="billData.car" clearable  style="margin-right:40px;"/>
            </el-form-item>
            <el-form-item label="司机" prop="driver">
                <el-input v-model="billData.driver" clearable />
            </el-form-item>
            <el-form-item label="经办记录人" prop="recorder" label-width="110px">
                <el-input v-model="billData.recorder" clearable />
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
      typeOption:[
        {
          label:'汽油',
          value:0
        },
        {
          label:'柴油',
          value:1
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
        intervalBeginTime:null,
        intervalEndTime: null,
        oilType:null      
        },
      time:null,

      // 新增弹窗
      dialogOilVisible: false,

      billData: {
        createTime: null,
        driver:null,
        oilType:null,
        recorder:null,
        oilDensity:null,
        oilVolume:null,
        oilWeight:null,
        car:null
      },
      billRules: {
        createTime: [
          { required: true, message: '日期不能为空', trigger: 'blur' }
        ],
         recorder: [
          { required: true, message: '经办记录人不能为空', trigger: 'blur' }
        ],
        oilType: [
          { required: true, message: '请先选择油品', trigger: 'change' }
        ],
        oilDensity: [
          { required: true, message: '密度不能为空', trigger: 'change' }
        ],
        oilVolume: [
          { required: true, message: '体积不能为空', trigger: 'change' }
        ],
        oilWeight: [
          { required: true, message: '重量不能为空', trigger: 'change' }
        ],
        car: [
          { required: true, message: '车辆不能为空', trigger: 'blur' }
        ],
        driver: [
          { required: true, message: '司机不能为空', trigger: 'blur' }
        ]
      }
    }
  },
  filters:{
    showType(val){
      switch(parseInt(val)){
        case 0 :
          return '汽油'
        case 1 :
          return '柴油'
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
      fuel.groundList(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.list = r.data.records
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
      this.listQuery.page = 1
      this.getList()
    },
    clearTime(){
      if(!this.time){
        this.listQuery.intervalBeginTime = null
        this.listQuery.intervalEndTime = null
        this.listQuery.page = 1
        this.getList()
      }
    },

    // 重置新增列表
    resetFormList() {
      this.billData = {
        createTime: null,
        driver:null,
        oilType:null,
        recorder:null,
        oilDensity:null,
        oilVolume:null,
        oilWeight:null,
        car:null
      }
      this.$refs['billForm'].resetFields()
    },
    // 打开新增弹窗
    oilFuel() {
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
          message: '请填写完整加油信息',
          type: 'warning'
        })
      }else{
        fuel.groundFuel(this.billData).then((r) => {
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
