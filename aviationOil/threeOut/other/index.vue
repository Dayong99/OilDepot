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
      <el-button type="primary" icon="el-icon-s-order" @click="oilFuel">
        出库
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
      <el-table-column label="油料类型" align="center">
        <template slot-scope="scope">
          <span v-text="showOil(scope.row.oilType)"></span>
        </template>
      </el-table-column>
      <el-table-column label="初读数" align="center">
        <template slot-scope="scope">
          {{ scope.row.beforeLiquid }}
        </template>
      </el-table-column>
      <el-table-column label="终读数" align="center">
        <template slot-scope="scope">
          {{ scope.row.afterLiquid }}
        </template>
      </el-table-column>
      <el-table-column label="加油类型" align="center">
        <template slot-scope="scope">
          <span v-text="showType(scope.row.fuelType)"></span>
        </template>
      </el-table-column>  
      <el-table-column label="加油数量 (L)" align="center">
        <template slot-scope="scope">
          {{ scope.row.oilVolume }}
        </template>
      </el-table-column>  
      <el-table-column label="司机" align="center">
        <template slot-scope="scope">
          {{ scope.row.driver }}
        </template>
      </el-table-column>
      <el-table-column label="保管员" align="center">
        <template slot-scope="scope">
          {{ scope.row.keeper }}
        </template>
      </el-table-column>
      <el-table-column label="用油部门" align="center">
        <template slot-scope="scope">
          {{ scope.row.site }}
        </template>
      </el-table-column>
      <el-table-column label="车辆" align="center">
        <template slot-scope="scope">
          {{ scope.row.tankName }}
        </template>
      </el-table-column>
      <el-table-column label="密度" align="center">
        <template slot-scope="scope">
          {{ scope.row.density }}
        </template>
      </el-table-column>
      <el-table-column label="油车井号码" align="center">
        <template slot-scope="scope">
          {{ scope.row.wellnumber }}
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="getList" />

    <el-dialog :visible.sync="dialogOilVisible" title="出库" @close="cancleForm" top="30vh" width="900px">
      <el-form :inline="true" ref="billForm" label-position="right" :rules="billRules" :model="billData" label-width="110px" size="small">
        <el-row>
          <el-col :span="24">
            <el-form-item label="日期" prop="createTime">
                <el-date-picker
                v-model="billData.createTime"
                type="datetime"
                value-format="yyyy-MM-dd HH:mm:ss"
                :picker-options="pickerOptions"
                placeholder="选择日期"
                style="width:190px;">
                </el-date-picker>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="油料类型" prop="oilType">
                <el-select
                    clearable
                    placeholder="请选择"
                    v-model="billData.oilType"
                    style="margin-right:40px;"
                    disabled
                    >
                    <el-option
                      v-for="item in typeOption"
                      :key="item.baseValue"
                      :label="item.baseKey"
                      :value="Number(item.baseValue)"
                    />
                </el-select>
             </el-form-item>
             <el-form-item label="初读数" prop="beforeLiquid">
                <el-input-number v-model="billData.beforeLiquid" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="终读数" prop="afterLiquid" >
                <el-input-number v-model="billData.afterLiquid" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-form-item label="加油类型" prop="fuelType">
                <el-select
                    clearable
                    placeholder="请选择"
                    v-model="billData.fuelType"
                    style="margin-right:40px;"
                    >
                    <el-option
                      v-for="item in fuelOption"
                      :key="item.baseValue"
                      :label="item.baseKey"
                      :value="Number(item.baseValue)"
                    />
                </el-select>
             </el-form-item>
          <el-form-item label="加油数量 (L)" prop="oilVolume">
                <el-input-number v-model="billData.oilVolume" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
            
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="车辆" prop="vehicle">
               <el-select
                    clearable
                    placeholder="请选择"
                    v-model="billData.vehicle"
                     style="margin-right:40px;"
                    >
                    <el-option
                        v-for="item in carOption"
                        :key="item.id"
                        :label="item.site"
                        :value="item.id"
                    />
                </el-select>
            </el-form-item>
            <el-form-item label="司机" prop="driver">
                <el-select
                    clearable
                    placeholder="请选择"
                    v-model="billData.driver"
                    >
                    <el-option
                        v-for="item in personOption"
                        :key="item.userId"
                        :label="item.username"
                        :value="item.userId"
                    />
                </el-select>
            </el-form-item>
            <el-form-item label="保管员" prop="keeper">
                <el-select
                    clearable
                    placeholder="请选择"
                    v-model="billData.keeper"
                    >
                    <el-option
                        v-for="item in personOption"
                        :key="item.userId"
                        :label="item.username"
                        :value="item.userId"
                    />
                </el-select>
            </el-form-item>
            <el-form-item label="用油部门" prop="department">
                <!-- <el-select
                    clearable
                    placeholder="请选择"
                    v-model="billData.department"
                     style="margin-right:40px;"
                    >
                    <el-option
                        v-for="item in unitOption"
                        :key="item.id"
                        :label="item.site"
                        :value="item.id"
                    />
                </el-select> -->
                <el-input v-model="billData.department"  style="margin-right:40px;" clearable />
             </el-form-item>
             <el-form-item label="密度" prop="density" >
                <el-input-number v-model="billData.density" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="油车井号码" prop="wellnumber">
                <el-input v-model="billData.wellnumber" clearable />
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
      typeOption:[],
      fuelOption:[],
      unitOption:[],
      carOption:[],
      personOption:[],
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
        intervalEndTime:null
      },
      time:null,

      // 新增弹窗
      dialogOilVisible: false,

      billData: {
        createTime: null,
        oilType:2,
        beforeLiquid:null,
        afterLiquid:null,
        fuelType:1,
        oilVolume:null,
        driver:null,
        keeper:null,
        department:null,
        vehicle:null,
        density:null,
        wellnumber:null
      },
      billRules: {
        createTime: [
          { required: true, message: '日期不能为空', trigger: 'blur' }
        ],
         oilType: [
          { required: true, message: '请选择油料类型', trigger: 'change' }
        ],
        beforeLiquid: [
          { required: true, message: '初读数不能为空', trigger: 'blur' }
        ],
        afterLiquid: [
          { required: true, message: '终读数不能为空', trigger: 'blur' }
        ],
        fuelType: [
          { required: true, message: '请选择加油类型', trigger: 'change' }
        ],
        oilVolume: [
          { required: true, message: '加油数量不能为空', trigger: 'blur' }
        ],
        driver: [
          { required: true, message: '请选择司机', trigger: 'change' }
        ],
        keeper: [
          { required: true, message: '请选择保管员', trigger: 'change' }
        ],
        department: [
          { required: true, message: '用油部门不能为空', trigger: 'blur' }
        ],
        vehicle: [
          { required: true, message: '请选择车辆', trigger: 'change' }
        ],
        density: [
          { required: true, message: '密度不能为空', trigger: 'blur' }
        ],
        wellnumber: [
          { required: true, message: '油车井号码不能为空', trigger: 'blur' }
        ]
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
    this.getOtherFuel().then(data => {
      this.fuelOption = data
    })
    this.getOil().then(data => {
      this.typeOption = data
    })
    this.getPlace().then(data => {
      this.unitOption = data
    })
    this.getCar().then(data => {
      this.carOption = data
    })
    this.getPerson().then(data => {
      this.personOption = data
    })
    this.getList()
  },
  methods: {
     showType(val){
      for(let i = 0;i<this.fuelOption.length;i++){
        if(parseInt(val)===parseInt(this.fuelOption[i].baseValue)){
          return this.fuelOption[i].baseKey
        }
      }
    },
    showOil(val){
      for(let i = 0;i<this.typeOption.length;i++){
        if(parseInt(val)===parseInt(this.typeOption[i].baseValue)){
          return this.typeOption[i].baseKey
        }
      }
    },
    // 加载列表
    getList() {
      this.listLoading = true
      fuel.otherList(this.listQuery).then((r) => {
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
        createTime: null,
        oilType:2,
        beforeLiquid:null,
        afterLiquid:null,
        fuelType:1,
        oilVolume:null,
        driver:null,
        keeper:null,
        department:null,
        vehicle:null,
        density:null,
        wellnumber:null
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
          fuel.otherFuel(this.billData).then((r) => {
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
