<template>
  <div class="app-container">
    <div class="filter-container">
      <el-select 
        placeholder="飞机" 
        style="width: 220px;" 
        clearable
        @clear="handleFilter"
        v-model="listQuery.fighterNum">
        <el-option
          v-for="item in planeOption"
          :key="item.id"
          :label="item.fighterName"
          :value="item.id"
        />
      </el-select>
      <el-select 
        placeholder="位置" 
        style="width: 220px;" 
        clearable
        @clear="handleFilter"
        v-model="listQuery.site">
        <el-option
          v-for="item in placeOptions"
          :key="item.id"
          :label="item.site"
          :value="item.id"
        />
      </el-select>
      <el-select 
        placeholder="订单状态" 
        style="width: 110px;" 
        clearable
        @clear="handleFilter"
        v-model="listQuery.status">
        <el-option
          v-for="item in typeOptions"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        />
      </el-select>
      <el-date-picker
        type="date"
        placeholder="创建时间"
        value-format="yyyy-MM-dd"
        @change="clearTime"
        v-model="listQuery.createTime">
      </el-date-picker>
      <el-button v-waves class="filter-item" type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
      <el-row style="margin-top:30px;">
        <el-col style="width:250px;">
          <el-button v-waves class="filter-item" type="primary" plain @click="createTask('add')">
            加油任务
          </el-button>
          <el-button v-waves class="filter-item" type="primary" plain @click="createTask('return')">
            退油任务
          </el-button>
        </el-col>
        <el-col :span="6">
          <el-radio v-model="radio" label="add" @change="getTypeList">加油列表</el-radio>
          <el-radio v-model="radio" label="return" @change="getTypeList">退油列表</el-radio>
        </el-col>
      </el-row>
      
    </div>

    <h5>{{ title }}</h5>
    <el-table
      v-loading="listLoading"
      :data="list"
      border
      fit
      highlight-current-row
      style="width: 100%;"
    >
      <el-table-column label="ID" align="center" width="80">
        <template slot-scope="scope">
          <span>{{ scope.$index+1 }}</span>
        </template>
      </el-table-column>

      <el-table-column label="订单创建时间" align="center" width="200px">
        <template slot-scope="scope">
          <span>{{ scope.row.createTime }}</span>
        </template>
      </el-table-column>

      <el-table-column label="订单号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.id }}</span>
        </template>
      </el-table-column>

      <el-table-column label="飞机" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.fighterName }}</span>
        </template>
      </el-table-column>

      <el-table-column label="位置" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.site }}</span>
        </template>
      </el-table-column>

      <!-- <el-table-column label="北斗设备" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.number }}</span>
        </template>
      </el-table-column> -->

      <!-- <el-table-column label="订单类型" align="center" width="100px">
        <template slot-scope="scope">
          <el-tag :type="parseInt(scope.row.orderKind)===1?'':'success'" effect="plain">
            {{ scope.row.orderKind | showType}}
          </el-tag>
        </template>
      </el-table-column> -->

      <el-table-column label="订单状态" align="center" width="150px">
        <template slot-scope="scope">
          <el-tag :type="getStatus(scope.row.status)" style="width:70px;">
            {{ scope.row.status | showStatus}}
          </el-tag>
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="total>0" :total="total" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="getList" />

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="formVisible" width="700px" @close="cancleTask">
      <el-form ref="form" :model="task"  label-width="80px" label-position="left" :rules="rules">
        <el-form-item label="飞机" prop="aircraftId">
          <el-select 
            v-model="task.aircraftId" 
            placeholder="请选择" 
            style="width: 220px;" 
            clearable>
            <el-option
              v-for="item in planeOption"
              :key="item.id"
              :label="item.fighterName"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="位置" prop="orderPlace">
          <el-select 
            v-model="task.orderPlace" 
            placeholder="请选择" 
            style="width: 220px;" 
            clearable>
            <el-option
              v-for="item in placeOptions"
              :key="item.id"
              :label="item.site"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="北斗设备" prop="number">
          <el-select 
            v-model="task.number" 
            placeholder="请选择" 
            style="width: 220px;" 
            clearable>
            <el-option
              v-for="item in deviceOptions"
              :key="item.id"
              :label="item.number"
              :value="item.number"
            />
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitTask(dialogStatus)">提交</el-button>
        <el-button @click="cancleTask">取消</el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import waves from '@/directive/waves'
import Pagination from '@/components/Pagination' // Secondary package based on el-pagination
import task from '@/api/task'

export default {
  name: 'Device',
  components: { Pagination },
  directives: { waves },

  data() {
    return {
      radio:'add',
      list: [],
      total: 0,
      listLoading: false,
      // 查询
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        fighterNum:null,
        site:null,
        status:null,
        createTime:null
      },

      planeOption:[],
      placeOptions:[],
      deviceOptions:[],
      typeOptions:[{
        label:'未确认',
        value:0
      },
      {
        label:'确认',
        value:1
      },
      {
        label:'完成',
        value:2
      },
      {
        label:'错误订单',
        value:10
      },
      {
        label:'不需要',
        value:11
      },
      {
        label:'其他原因',
        value:12
      }],
      dialogStatus:'',
      textMap: {
        add: '加油任务',
        return: '退油任务'
      },
      formVisible:false,
      task:{
        aircraftId:null,
        orderPlace:null,
        number:null,
        orderType:1
      },
      rules: {
        aircraftId: [
          { required: true, message: '请选择飞机', trigger: 'change' }
        ],
         orderPlace: [
          { required: true, message: '请选择位置', trigger: 'change' }
        ],
        number: [
          { required: true, message: '请选择北斗设备', trigger: 'change' }
        ]
      }
    }
  },
  filters :{
    showType:function(val){
      switch (parseInt(val)) {
        case 1:
          return "定时送"
          break;
        case 2:
          return "及时送"
          break;
        default:
          return "无"
          break;
      }
    },
    showPriority:function(val){
      switch (parseInt(val)) {
        case 1:
          return "普通"
          break;
        case 2:
          return "紧急"
          break;
        default:
          return "无"
          break;
      }
    },
    showStatus:function(val){
      switch (parseInt(val)) {
        case 0:
          return "未确认"
          break;
        case 1:
          return "确认"
          break;
        case 2:
          return "完成"
          break;
        case 10:
          return "错误订单"
          break;
        case 11:
          return "不需要"
          break;
        case 1:
          return "其他原因"
          break;
        default:
          return "无"
          break;
      }
    }
  },
  created() {
    this.getList()
    this.getPlane().then(data => {
      this.planeOption = data
    })
    this.getPlace().then(data => {
      this.placeOptions = data
    })
    this.getDevice().then(data => {
      this.deviceOptions = data
    })
  },
  methods: {
    getStatus(val){
      switch (parseInt(val)) {
        case 0:
          return "info"
          break;
        case 1:
          return ""
          break;
        case 2:
          return "success"
          break;
        case 10:
          return "danger"
          break;
        case 11:
          return "danger"
          break;
        case 12:
          return "danger"
          break;
        default:
          return "无"
          break;
      }
    },

    getTypeList(){
      console.log(this.checkList)
      this.getList()
    },

    getAddList(){
      this.title = '加油列表'
        task.taskList(this.listQuery).then((r) => {
          if (r.code === 20000) {
            this.list = r.data.rows
            this.total = r.data.total
            this.listLoading = false
          }
        }).catch(() => {
          this.listLoading = false
        })    
    },
    getReturnList(){
      this.title = '退油列表'
        task.returnList(this.listQuery).then((r) => {
          if (r.code === 20000) {
            this.list = r.data.rows
            this.total = r.data.total
            this.listLoading = false
          }
        }).catch(() => {
          this.listLoading = false
        }) 
    },
    getList() {
      this.listLoading = true
      console.log(this.radio)
      if(this.radio==='add'){
        this.getAddList()
      }else if (this.radio==='return'){
        this.getReturnList()
      }
      
    },
    handleFilter() {
      this.listQuery.pageNum = 1
      this.getList()
    },
    clearTime(){
      if(!this.listQuery.querydate){
        this.handleFilter()
      }
    },
    resetTask(){
      this.task = {
        aircraftId:null,
        orderPlace:null,
        number:null,
        orderType:1
      }
    },
    createTask(val){
      this.formVisible = true
      this.dialogStatus = val
    },
    submitTask(){
      console.log(this.dialogStatus)
      this.$refs['form'].validate((valid) => {
        if (valid) {
          if(this.dialogStatus==='add'){
            task.createAdd(this.task).then((r) => {
              this.formVisible = false
              this.$notify({
                title: '成功',
                message: r.data,
                type: 'success',
                duration: 2000
              })
              this.getList()
            }).catch(e => {
              this.listLoading = false
            })
          }else if(this.dialogStatus==='return'){
            task.createReduce(this.task).then((r) => {
              this.formVisible = false
              this.$notify({
                title: '成功',
                message: r.data,
                type: 'success',
                duration: 2000
              })
              this.getList()
            }).catch(e => {
              this.listLoading = false
            })
          }
          
        }
      })
    },
    cancleTask(){
      this.formVisible = false
      this.resetTask()
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
.el-row {
  margin-bottom:20px;
}
.el-col span:first-child {
  width:80px;
  display: inline-block;
}
.el-col span:last-child {
  text-align:right;
}
.el-col .oilname {
  width:150px!important;
}
.el-row span:nth-child(3){
  text-align: right;
}
h4 i {
  margin-right:5px;
  color:#66b1ff;
}
.el-tag {
  width:58px;
}
.el-radio {
  margin-top:13px;
}
</style>
