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
      <el-button type="primary" icon="el-icon-s-order" @click="returnOil">
        退油
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
      <!-- <el-table-column label="进出" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.inOutType===0?'进':'出' }}</span>
        </template>
      </el-table-column> -->
      <el-table-column label="温度" align="center">
        <template slot-scope="scope">
          {{ scope.row.temperature===undefined?'':scope.row.temperature+' ℃' }}
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

    <el-dialog :visible.sync="dialogOilVisible" title="退油车退油" width="1160px" height="50%" @close="cancleForm" top="30vh">
      <el-form :inline="true" ref="billForm" label-position="right" :rules="billRules" :model="billData" label-width="92px" size="small">
        <el-row>
          <el-col :span="24">
            <el-form-item label="日期" prop="createTime" label-width="110px">
                <el-date-picker
                v-model="billData.createTime"
                type="datetime"
                placeholder="选择日期"
                value-format="yyyy-MM-dd HH:mm:ss"
                :picker-options="pickerOptions1"
                style="width:190px;">
                </el-date-picker>
            </el-form-item>
            <el-form-item label="凭证" prop="proof">
                <el-input v-model="billData.proof" clearable />
            </el-form-item>
            <el-form-item label="罐车测量数" prop="measuring" label-width="110px">
                <el-input-number v-model="billData.measuring" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>
            <el-form-item label="温度 (℃)" prop="temperature">
                <el-input-number v-model="billData.temperature" label="描述文字"></el-input-number>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="经办记录人" prop="recorder" label-width="110px">
                <el-input v-model="billData.recorder" clearable  style="width:190px;"/>
            </el-form-item>
            <el-form-item label="油罐车" prop="vehicleId" label-width="92px">
               <el-select
                v-model="billData.vehicleId"
                placeholder="请选择"
                clearable
                >
                <el-option
                    v-for="item in carOption"
                    :key="item.id"
                    :label="item.tankName"
                    :value="item.id"
                />
                </el-select>
            </el-form-item>
            <el-form-item label="油罐" prop="tankId" label-width="110px">
               <el-select
                v-model="billData.tankId"
                placeholder="请选择"
                clearable
                style="width:150px;"
                >
                <el-option
                    v-for="item in tankOption"
                    :key="item.id"
                    :label="item.tankName"
                    :value="item.id"
                />
                </el-select>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="测量前" prop="measuringBefore" style="margin-right:50px;" label-width="110px">
              <el-input-number v-model="billData.measuringBefore" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="测量后" prop="measuringAfter"  label-width="92px">
              <el-input-number v-model="billData.measuringAfter" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="读取前" prop="iotBefore" label-width="110px">
              <el-input-number v-model="billData.iotBefore" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="读取后" prop="iotAfter">
              <el-input-number v-model="billData.iotAfter" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
            
          </el-col>
        </el-row>
        <!-- <el-row class="rNumber">
          <el-col :span="24">    
            <el-form-item label="密度：" prop="oilDensity">
                <el-input-number v-model="billData.oilDensity" :min="0" label="描述文字" style="margin-right:40px;"></el-input-number>
            </el-form-item>
            <el-form-item label="体积：" prop="oilVolume">
                <el-input-number v-model="billData.oilVolume" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
            <el-form-item label="重量：" prop="oilWeight"  label-width="110px">
                <el-input-number v-model="billData.oilWeight" :min="0" label="描述文字"></el-input-number>
            </el-form-item>
          </el-col>
        </el-row> -->
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitForm()">提交</el-button>
        <el-button @click="cancleForm">取消</el-button>
      </div>
    </el-dialog>

    <el-dialog title="退油详情" :visible.sync="tableVisible" class="detail">
      <el-row>
        <el-col :span="24"><span>日期：</span>{{ info.createTime }}</el-col>
        <el-col :span="24"><span>单号：</span>{{ info.proof }}</el-col>
        <el-col :span="24"><span>罐车测量数：</span>{{ info.measuring }}</el-col>
        <el-col :span="24"><span>温度：</span>{{ info.temperature===undefined?'':info.temperature+' ℃' }}</el-col>
        <el-col :span="24"><span>经办记录人：</span>{{ info.recorder }}</el-col>
        <el-col :span="24"><span>油罐：</span>{{ info.tankName }}</el-col>
        <el-col :span="24"><span>进出：</span>{{ info.inOutType===0?'进油':'出油' }}</el-col>
      </el-row>
      <el-row>
        <el-col :span="6"><span>测量前：</span>{{ info.measuringBefore }}</el-col>
        <el-col :span="6"><span>测量后：</span>{{ info.measuringAfter }}</el-col>
      </el-row>
      <el-row>
        <el-col :span="6"><span>密度：</span>{{ info.oilDensity }}</el-col>
        <el-col :span="6"><span>体积：</span>{{ info.oilVolume }} L</el-col>
        <el-col :span="6"><span>重量：</span>{{ info.oilWeight }} kg</el-col>
      </el-row>
      <!-- <el-row>
        <el-col :span="6"><span>单号：</span>{{ info.proof }}</el-col>
        <el-col :span="6"><span>罐车测量数：</span>{{ info.measuring }}</el-col>
        <el-col :span="6"><span>温度：</span>{{ info.temperature===undefined?'':info.temperature+'℃' }}</el-col>
        <el-col :span="6"><span>经办记录人：</span>{{ info.recorder }}</el-col>
      </el-row>
      <el-row>
        <el-col :span="6"><span>油罐：</span>{{ info.tankName }}</el-col>
        <el-col :span="6"><span>进出：</span>{{ info.inOutType===0?'进':'出' }}</el-col>
        <el-col :span="6"><span>测量前：</span>{{ info.measuringBefore }}</el-col>
        <el-col :span="6"><span>测量后：</span>{{ info.measuringAfter }}</el-col>
      </el-row>
      <el-row>
        <el-col :span="6"><span>密度：</span>{{ info.oilDensity }}</el-col>
        <el-col :span="6"><span>体积：</span>{{ info.oilVolume }}</el-col>
        <el-col :span="6"><span>重量：</span>{{ info.oilWeight }}</el-col>
      </el-row> -->
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
      carOption:[],
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
      listQuery: {
        pageSize: 10,
        page: 1,
        beginTime:null,
        endTime: null,
        scoreId: null
      },
      time:null,

      // 新增弹窗
      dialogOilVisible: false,

      billData: {
        createTime: null,
        proof: null,
        measuring: null,
        recorder:null,
        temperature:null,
        measuringBefore: null,
        measuringAfter: null,
        inOutType :0,
        iotBefore:null,
        iotAfter:null,
        vehicleId:null,
        tankId:null
      },
      billRules: {
       createTime: [
          { required: true, message: '日期不能为空', trigger: 'blur' }
        ],
        proof: [
          { required: true, message: '凭证不能为空', trigger: 'blur' }
        ],
        tankId: [
          { required: true, message: '请选择油罐', trigger: 'change' }
        ],
        // measuringBefore: [
        //   { required: true, message: '测量前液位不能为空', trigger: 'change' }
        // ],
        // measuringAfter: [
        //   { required: true, message: '测量前液位不能为空', trigger: 'change' }
        // ],
        // iotBefore: [
        //   { required: true, message: '读取前不能为空', trigger: 'change' }
        // ],
        // iotAfter: [
        //   { required: true, message: '读取后不能为空', trigger: 'change' }
        // ],
        measuring: [
          { required: true, message: '罐车测量数不能为空', trigger: 'change' }
        ],
        recorder: [
          { required: true, message: '经办记录人不能为空', trigger: 'blur' }
        ],
        vehicleId: [
          { required: true, message: '请选择油罐车', trigger: 'change' }
        ]
      },
      // 详情
      tableVisible: false,
      info: {}
    }
  },
  created(){
    this.getTank().then(data => {
      this.tankOption = data
    })
     this.getCar().then(data => {
       data.forEach((item,index)=>{
         if(item.oilStock>0){
           this.carOption.push(item)
         }
       })
      //  console.log(this.carOption)
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
    // 加载列表
    getList() {
      this.listLoading = true
      fuel.returnList(this.listQuery).then((r) => {
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
    },
    // 重置新增列表
    resetFormList() {
      this.billData = {
        createTime: null,
        proof: null,
        measuring: null,
        recorder:null,
        temperature:null,
        measuringBefore: null,
        measuringAfter: null,
        inOutType :0,
        iotBefore:null,
        iotAfter:null,
        vehicleId:null,
        tankId:null
      }
      this.$refs['billForm'].resetFields()
    },
    // 打开新增弹窗
    returnOil() {
      this.dialogOilVisible = true
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

      if (this.infoFlag === false) {
        this.$message({
          message: '请填写完整退油信息',
          type: 'warning'
        })
      }else{
        fuel.returnFuel(this.billData).then((r) => {
          if (r.code === 20000) {
            this.dialogOilVisible = false
            this.$message({
              message: '退油车退油成功',
              type: 'success'
            })
            this.resetFormList()
            this.getList()
          }
        }).catch(() => {
          setTimeout(() => {
            this.$message({
              message: r.data,
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
    },
    // 查看详情
    showDetail(row) {
      this.tableVisible = true
      this.info = row
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
.detail .el-row .el-col{
  margin-bottom:20px;
}
.tem {
  margin-left:5px;
}

</style>
