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
        v-model="listQuery.scoreId"
        style="width: 200px;"
        placeholder="单号"
        clearable
        class="filter-item"
        @clear="handleFilter"
        @keyup.enter.native="handleFilter"
      />
      <el-button type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
      <el-button type="primary" icon="el-icon-s-order" @click="dischargeOil">
        收油
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
      <el-table-column label="单号" align="center">
        <template slot-scope="scope">
          {{ scope.row.proof }}
        </template>
      </el-table-column>
      <el-table-column label="罐车测量数" align="center">
        <template slot-scope="scope">
          {{ scope.row.measuring }}
        </template>
      </el-table-column>
      <el-table-column label="温度" align="center">
        <template slot-scope="scope">
          {{ scope.row.temperature===undefined?'':scope.row.temperature+' ℃'}}
        </template>
      </el-table-column>
      <el-table-column label="经办记录人" align="center">
        <template slot-scope="scope">
          {{ scope.row.recorder }}
        </template>
      </el-table-column>
      
      <el-table-column label="操作" align="center" width="150">
        <template slot-scope="{row}">
          <el-button type="primary" plain size="mini" @click="showDetail(row)">详情</el-button>
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="listTotal>0" :total="listTotal" :page.sync="listQuery.page" :limit.sync="listQuery.pageSize" @pagination="getList" />

    <el-dialog :visible.sync="dialogOilVisible" title="铁路油罐车收油" width="1240px" height="50%" @close="cancleForm">
      <el-form :inline="true" ref="billForm" label-position="right" :rules="billRules" :model="billData" label-width="70px" size="small">
        <el-row>
          <el-col :span="24">
            <el-form-item label="日期" prop="createTime">
                <el-date-picker
                v-model="billData.createTime"
                type="datetime"
                value-format="yyyy-MM-dd HH:mm:ss"
                :picker-options="pickerOptions1"
                style="width:190px;"
                placeholder="选择日期">
                </el-date-picker>
            </el-form-item>
            <el-form-item label="凭证" prop="proof">
                <el-input v-model="billData.proof" clearable  style="width:115px;"/>
            </el-form-item>
            <el-form-item label="罐车测量数" prop="measuring" label-width="110px">
                <el-input-number v-model="billData.measuring" :min="0"  label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="温度 (℃)" prop="temperature">
                <el-input-number v-model="billData.temperature" label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="经办记录人" prop="recorder" label-width="110px">
                <el-input v-model="billData.recorder" clearable style="width:115px;"/>
            </el-form-item>
            <el-form-item label="油罐">
               <el-select
                v-model="billData.tankId"
                placeholder="请选择"
                clearable
                @change="showData"
                @clear="clearForm"
                >
                <el-option
                    v-for="item in tankOption"
                    :key="item.key"
                    :label="item.tankName"
                    :value="item.id"
                />
                </el-select>
            </el-form-item>
            <el-button type="success" plain icon="el-icon-plus" size="small" @click="addOilList">新增</el-button>
          </el-col>
        </el-row>
      </el-form>
      <el-divider v-if="dataShow"/>
      <h3 v-if="dataShow">油罐测量数据</h3>
      <el-form
        v-for="(item, index) in formList"
        :key="index"
        :ref="'oilForm'+index"
        :inline="true"
        :rules="dataRules"
        :model="formList[index]"
        label-position="right"
        label-width="65px"
        size="small"
        class="oilForm"
        v-if="dataShow"
      >
        <el-form-item label="油罐" prop="tankId">
          <span v-text="showTank(item.tankId)" style="width:60px;display:inline-block;"></span>
        </el-form-item>
        <el-form-item label="测量前" prop="measuringBefore" label-width="80px">
          <el-input-number v-model="item.measuringBefore" :min="0"  label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="测量后" prop="measuringAfter" label-width="80px">
          <el-input-number v-model="item.measuringAfter" :min="0"  label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="读取前" prop="iotBefore" label-width="80px">
          <el-input-number v-model="item.iotBefore" :min="0"  label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="读取后" prop="iotAfter" label-width="80px">
          <el-input-number v-model="item.iotAfter" :min="0"  label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="进出" prop="inOut">
          <el-select
              placeholder=""
              v-model="item.inOut"
              clearable
              style="width:80px;"
            >
              <el-option
                v-for="item in directionOption"
                :key="item.value"
                :label="item.label"
                :value="item.value"
              />
            </el-select>
        </el-form-item>
        <el-form-item>
          <i class="el-icon-circle-close" @click.prevent="deleteOil(index)"></i>
        </el-form-item>        
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitForm()">提交</el-button>
        <el-button @click="cancleForm">取消</el-button>
      </div>
    </el-dialog>

    <el-dialog title="收油详情" :visible.sync="tableVisible" width="1000px">
      <el-row class="info">
        <el-col :span="6">日期：<span>{{ info.createTime }}</span></el-col>
        <el-col :span="6">单号：<span>{{ info.proof }}</span></el-col>
        <el-col :span="6">罐车测量数：<span>{{ info.measuring }}</span></el-col>
        <el-col :span="6">温度：<span>{{ info.temperature===undefined?'':info.temperature+' ℃'}}</span></el-col>
        <el-col :span="6">经办记录人：<span>{{ info.recorder }}</span></el-col>
      </el-row>
      <el-table
        :data="tableList"
        fit
        highlight-current-row
        style="margin-top:20px;"
      >
        <el-table-column align="center" label="ID" width="50">
          <template slot-scope="scope">
            {{ scope.$index+1 }}
          </template>
        </el-table-column>
        <el-table-column label="油罐" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.tankName }}</span>
          </template>
        </el-table-column>
        <el-table-column label="测量前液位" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.measuringBefore }}</span>
          </template>
        </el-table-column>
        <el-table-column label="测量后液位" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.measuringAfter }}</span>
          </template>
        </el-table-column>
        <el-table-column label="读取液位(前)" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.iotBefore }}</span>
          </template>
        </el-table-column>
        <el-table-column label="读取液位(后)" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.iotAfter }}</span>
          </template>
        </el-table-column>
        <el-table-column label="进出" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.inOutType===0?'进油':'出油' }}</span>
          </template>
        </el-table-column>
        <el-table-column label="密度" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.oilDensity }}</span>
          </template>
        </el-table-column>
        <el-table-column label="体积 (L)" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.oilVolume }}</span>
          </template>
        </el-table-column>
        <el-table-column label="重量 (kg)" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.oilWeight }}</span>
          </template>
        </el-table-column>
      </el-table>
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
      tankOption:[],
      directionOption:[
          {
              value:0,
              label:'进油'
          },
          {
              value:1,
              label:'出油'
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
      time:null,
      listQuery: {
        pageSize: 10,
        page: 1,
        beginTime:null,
        endTime: null,
        scoreId: null
      },

      // 新增弹窗
      dataShow:false,
      tankFlag : 0,
      dialogOilVisible: false,

      formList: [{
        tankId:null,
        measuringBefore: null,
        measuringAfter: null,
        iotBefore: null,
        iotAfter: null,
        inOut :0,
        oilDensity:0,
        oilVolume:0,
        oilWeight:0
      }],
      params: [],
      obj: {},
      formObj: {},
      // 表单验证
      dischargeFlag: false,
      dischargeFlagArr: [],
      infoFlag: false,
      formArr: [],

      billData: {
        createTime: null,
        proof: null,
        measuring: null,
        recorder:null,
        temperature:null,
        tankId:null
      },
      billRules: {
        createTime: [
          { required: true, message: '日期不能为空', trigger: 'blur' }
        ],
        proof: [
          { required: true, message: '凭证不能为空', trigger: 'blur' }
        ],
        // tankId: [
        //   { required: true, message: '请选择油罐', trigger: 'change' }
        // ],
        measuring: [
          { required: true, message: '罐车测量数不能为空', trigger: 'change' }
        ],
        recorder: [
          { required: true, message: '经办记录人不能为空', trigger: 'blur' }
        ]
      },
      dataRules: {
        measuringBefore: [
          { required: true, message: '测量前液位不能为空', trigger: 'change' }
        ],
        measuringAfter: [
          { required: true, message: '测量前液位不能为空', trigger: 'change' }
        ],
        inOut: [
          { required: true, message: '请选择进出', trigger: 'change' }
        ]
      },

      // 详情
      tableVisible: false,
      tableList: [],
      info: {}
    
    }
  },
  created(){
    this.getTank().then(data => {
      this.tankOption = data
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
    showTank(val){
      for(let i = 0;i<this.tankOption.length;i++){
        if(val===this.tankOption[i].id){
          return this.tankOption[i].tankName
        }
      }
    },
    // 加载列表
    getList() {
      this.listLoading = true
      fuel.dischargeList(this.listQuery).then((r) => {
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

    // 卸油
    // 重置新增列表
    resetFormList() {
      this.dataShow = false
      this.tankFlag = 0
      this.billData = {
        createTime: null,
        proof: null,
        measuring: null,
        recorder:null,
        temperature:null,
        tankId:null
      }
      this.formList = [
        {
          tankId:null,
          measuringBefore: null,
          measuringAfter: null,
          iotBefore: null,
          iotAfter: null,
          inOut :0,
          oilDensity:0,
          oilVolume:0,
          oilWeight:0
        }
      ]
      this.$refs['billForm'].resetFields()
      const len = document.querySelectorAll('.oilForm').length
      for (let i = 0; i < len; i++) {
        this.formArr.push('oilForm' + i)
      }
      this.formArr.forEach((item, index) => {
        if(this.$refs[item][0]){
          this.$refs[item][0].resetFields()
        }
      })
      
    },
    // 打开新增弹窗
    dischargeOil() {
      this.dialogOilVisible = true
    },      
    // 显示测量数据
    showData(){
      if(this.billData.tankId&&this.tankFlag===0){
        this.dataShow = true;
        this.formList = [
          {
            tankId:this.billData.tankId ,
            measuringBefore: null,
            measuringAfter: null,
            iotBefore: null,
            iotAfter: null,
            inOut :0,
            oilDensity:0,
            oilVolume:0,
            oilWeight:0
          }
        ]
        this.tankFlag = 1
      }else if(this.billData.tankId){
        this.dataShow = true;
        // this.formList.forEach((item,index)=>{
        //   item.tankId = this.billData.tankId 
        // })
      }
    },
    clearForm(){
      // this.dataShow = false
      // this.tankFlag = 0
      // this.formList = [
      //   {
      //     tankId:null,
      //     measuringBefore: null,
      //     measuringAfter: null,
      //     iotBefore: null,
      //     iotAfter: null,
      //     inOut :0,
      //     oilDensity:0,
      //     oilVolume:0,
      //     oilWeight:0
      //   }
      // ]
    },
    // 新增油品条目
    addOilList() {
      if(this.dataShow&&this.billData.tankId){
        this.formList.push({
          tankId:null,
          measuringBefore: null,
          measuringAfter: null,
          iotBefore: null,
          iotAfter: null,
          inOut :0,
          oilDensity:0,
          oilVolume:0,
          oilWeight:0
        })
        // 添加条目的油罐为所选择的
        let len = this.formList.length - 1
        this.formList[len].tankId = this.billData.tankId 
      }else{
        this.$message.warning('请选择油罐')
      }
    },
    // 删除油品条目
    deleteOil(index) {
      this.formList.splice(index, 1)
      if(this.formList.length===0){
        this.dataShow = false
        this.tankFlag = 0
        this.billData.tankId = null
      }
    },
   
    // 提交表单
    submitForm() {
      // 验证支拨单表单
      this.$refs['billForm'].validate((valid) => {
        if (valid) {
          this.infoFlag = true
        } else { // 验证不通过
          this.infoFlag = false
          return false
        }
      })
      // 验证油罐测量数据信息
      this.formArr = []
      const len = document.querySelectorAll('.oilForm').length
      for (let i = 0; i < len; i++) {
        this.formArr.push('oilForm' + i)
      }
      this.formArr.forEach((item, index) => {
        this.$refs[item][0].validate((valid) => {
          if (valid) {
            this.dischargeFlag = true
          } else {
            return false
          }
        })
      })

      // 处理参数
      this.params = []
      this.formList.forEach((item, index) => {
        this.formObj = {}
        this.formObj = item
        this.params.push(this.formObj)
      })

      // 提交信息
      if (this.infoFlag === false) {
        this.$message({
          message: '请填写完整作业信息',
          type: 'warning'
        })
      }else if (this.dischargeFlag === false) {
        this.$message({
          message: '请填写完整油罐测量数据',
          type: 'warning'
        })
      } else {
        const formParams = {
          'createTime': this.billData.createTime,
          'proof': this.billData.proof,
          'measuring': this.billData.measuring,
          'recorder': this.billData.recorder,
          'temperature': this.billData.temperature,
          'unloadingdetails': this.params
        }
        fuel.dischargeFuel(formParams).then((r) => {
          if (r.code === 20000) {
            this.dialogOilVisible = false
            this.$message({
              message: '收油成功',
              type: 'success'
            })
            this.resetFormList()
            this.getList()
          }
        }).catch(() => {
          setTimeout(() => {
            this.$message({
              message: '收油失败',
              type: 'error'
            })
          }, 500)
        })
      }
      // console.log(this.formList)
    },
    // 取消
    cancleForm() {
      this.dialogOilVisible = false
      this.resetFormList()
    },

    // 查看详情
    showDetail(row) {
      this.tableVisible = true
      this.getTableList(row)
      this.info = row
    },
    // 加载详情列表
    getTableList(row) {
      fuel.dischargeDetail({
        unloadId: row.id
      }
      ).then((r) => {
        if (r.code === 20000) {
          this.tableList = r.data
        }
      }).catch(() => {
      })
    }
  }
}
</script>

<style scoped>
.filter-container {
  margin-bottom:30px;
}
.el-select {
    width:100px;
}
.el-input {
    width:150px;
}
.el-input-number {
    width:125px;
}
.el-form-item i{
    color:#909399;
    font-size:16px;
}
.info .el-col {
  margin-bottom:20px;
}
h3 {
    margin-left:10px;
}
.tem {
  margin-left:5px;
}
</style>
