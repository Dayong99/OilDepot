<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.craneNum" 
      placeholder="鹤位代码" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-input 
      v-model="listQuery.craneName" 
      placeholder="鹤位名称" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-button class="filter-item" type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
      <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="handleCreate">
        添加
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
      <el-table-column label="鹤位代码" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.craneNum }}</span>
        </template>
      </el-table-column>
       <el-table-column label="鹤位名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.craneName }}</span>
        </template>
      </el-table-column>
      <el-table-column label="鹤位位置" align="center">
        <template slot-scope="scope">
          <span v-text="showPosition(scope.row.cranePostion)"></span>
        </template>
      </el-table-column>
      <el-table-column label="油品" align="center">
        <template slot-scope="scope">
          <span v-text="showOil(scope.row.oilType)"></span>
        </template>
      </el-table-column>
      <el-table-column label="连接油罐" align="center">
        <template slot-scope="scope">
          <span v-text="showTank(scope.row.oilTank)"></span>
        </template>
      </el-table-column>
      <el-table-column label="开关状态" align="center" width="100">
        <template slot-scope="scope">
          <svg-icon :icon-class="parseInt(scope.row.isOpen)===0?'close':'open'"/>
        </template>
      </el-table-column>
      <el-table-column label="使用状态" align="center" width="100">
        <template slot-scope="scope">
          <i :class="parseInt(scope.row.isUse)===0?'el-icon-success isUse':'el-icon-error noUse'" />
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center" width="200" class-name="small-padding fixed-width">
        <template slot-scope="{row}">
          <el-button type="primary" size="mini" @click="handleUpdate(row)">
            编辑
          </el-button>
          <el-button size="mini" type="danger" @click="handleModifyStatus(row,'deleted')">
            删除
          </el-button>
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="total>0" :total="total" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="getList" />

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" width="700px" @close="cancleDialog">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="120px">
        <el-form-item label="鹤位代码" prop="craneNum">
          <el-input v-model="temp.craneNum" clearable />
        </el-form-item>
        <el-form-item label="鹤位名称" prop="craneName">
          <el-input v-model="temp.craneName" clearable />
        </el-form-item>
        <el-form-item label="鹤位位置" prop="cranePostion">
           <el-select 
           v-model="temp.cranePostion" 
           class="filter-item" 
           placeholder="请选择" 
           clearable >
           <el-option
                  v-for="item in positionOption"
                  :key="parseInt(item.baseValue)"
                  :label="item.baseKey"
                  :value="parseInt(item.baseValue)"
                />
          </el-select>
        </el-form-item>
        <el-form-item label="油品" prop="oilType">
           <el-select 
           v-model="temp.oilType" 
           class="filter-item" 
           placeholder="请选择" 
           clearable >
            <el-option
                  v-for="item in oilOption"
                  :key="parseInt(item.baseValue)"
                  :label="item.baseKey"
                  :value="parseInt(item.baseValue)"
                />
          </el-select>
        </el-form-item>
        <el-form-item label="连接油罐" prop="oilTank">
           <el-select 
           v-model="temp.oilTank" 
           class="filter-item" 
           placeholder="请选择" 
           multiple
           filterable
           default-first-option
           clearable >
            <el-option 
            v-for="(item,index) in tankOption" 
            :key="index" 
            :label="item.tankName" 
            :value="item.id" />
          </el-select>
        </el-form-item>
        <el-form-item label="开关状态" prop="isOpen">
          <el-radio-group v-model="temp.isOpen">
            <el-radio label="1">开</el-radio>
            <el-radio label="0">关</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="使用状态" prop="isUse">
          <el-radio-group v-model="temp.isUse">
            <el-radio label="0">在用</el-radio>
            <el-radio label="1">停用</el-radio>
          </el-radio-group>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogStatus==='create'?createData():updateData()">
          确认
        </el-button>
        <el-button @click="cancleDialog">
          取消
        </el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import Pagination from '@/components/Pagination'
import system from '@/api/systemManagement'
import { parseTime } from '@/utils'

export default {
  components: { Pagination },
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: 'success',
        draft: 'info',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
      list: null,
      total: 0,

      tankOption:[],
      positionOption:[],
      oilOption:[],
      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        craneNum: null,
        craneName: null
      },
      statusOptions: ['published', 'draft', 'deleted'],
      temp: {
        id: null,
        craneNum: null,
        craneName: null,
        oilType: null,
        oilTank: null,
        isUse: null,
        isOpen: null,
        cranePostion:null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        craneNum: [
          { required: true, message: '鹤位代码不能空', trigger: 'blur' }
        ],
        oilType: [
          { required: true, message: '请选择油品', trigger: 'change' }
        ],
        craneName: [
          { required: true, message: '鹤位名称不能空', trigger: 'blur' }
        ],
        oilTank: [
          { required: true, message: '请选择连接油罐', trigger: 'change' }
        ],
        isOpen: [
          { required: true, message: '请选择开关状态', trigger: 'change' }
        ],
        isUse: [
          { required: true, message: '请选择使用状态', trigger: 'change' }
        ],
        cranePostion: [
          { required:true, message: '请选择鹤位位置', trigger:'change' }
        ]
      }
    }
  },
  computed: {
  },
  created() {
    this.getList()
     this.getTank().then(data => {
      this.tankOption = data
    })
     this.getCranePosition().then(data => {
      this.positionOption = data
    })
    this.getOil().then(data => {
      this.oilOption = data
    })
  },
  methods: {
      showTank(val){
          let str = ''
          let arr
        if(val.indexOf(',')!==-1){
           arr = val.split(',')
        }else{
            arr = [val]
        }
          arr.forEach((item,index)=>{
              this.tankOption.forEach((item1,index1)=>{
                  if(parseInt(item)===parseInt(item1.id)){
                      if(index>0){
                        str += '，'+item1.tankName
                      }else {
                        str += item1.tankName
                      }
                      
                  }
              })
          })
          return str
      },
    showPosition(val){
      for(let i=0;i<this.positionOption.length;i++){
        if (parseInt(this.positionOption[i].baseValue) === val) {
          return this.positionOption[i].baseKey
        }
      }
    },
    showOil(val){
      for(let i=0;i<this.oilOption.length;i++){
        if (parseInt(this.oilOption[i].baseValue) === parseInt(val)) {
          return this.oilOption[i].baseKey
        }
      }
    },
    getList(val) {
      this.listLoading = true
      system.getCrane(this.listQuery).then((r) => {
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
    },
    resetTemp() {
      this.temp = {
        id: null,
        craneNum: null,
        craneName: null,
        oilType: null,
        oilTank: null,
        isUse: null,
        isOpen: null,
        cranePostion:null
      }
    },
    // 创建
    handleCreate() {
      this.resetTemp()
      this.dialogStatus = 'create'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    createData() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          system.addCrane(this.temp).then((r) => {
            this.dialogFormVisible = false
             this.temp.id = r.data
             this.temp.oilTank = this.temp.oilTank.toString()
              this.list.push(this.temp)
              this.$notify({
                title: '成功',
                message: '创建成功',
                type: 'success',
                duration: 2000
              })
          }).catch(e => {
            this.listLoading = false
          })
        }
      })
    },
    // 更新
    handleUpdate(row) {
      this.temp = Object.assign({}, row) // copy obj
      this.dialogStatus = 'update'
      this.dialogFormVisible = true
      this.temp.oilType = Number(this.temp.oilType)
      this.temp.isUse = this.temp.isUse.toString()
      this.temp.isOpen = this.temp.isOpen.toString()
        if(this.temp.oilTank.indexOf(',')!==-1){
            let arr = []
            this.temp.oilTank= this.temp.oilTank.split(',')
            this.temp.oilTank.forEach((item,index)=>{
                arr.push(Number(item))
            })
            this.temp.oilTank = arr
        }else{
        this.temp.oilTank = [Number(this.temp.oilTank)]
        }
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    updateData() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          system.modifyCrane(this.temp).then((r) => {
            this.temp.oilTank = this.temp.oilTank.toString()
            for (const v of this.list) {
              if (v.id === this.temp.id) {
                const index = this.list.indexOf(v)
                this.list.splice(index, 1, this.temp)
                break
              }
            }
            this.dialogFormVisible = false
            this.$notify({
              title: '成功',
              message: '更新成功',
              type: 'success',
              duration: 2000
            })
          }).catch(e => {
            this.listLoading = false
          })
        }
      })
    },
    // 删除
    handleDelete(row) {
      this.$confirm('此操作将永久删除该条数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning',
          center: true
        }).then(() => {
          const index = this.list.indexOf(row)
          system.deleteCrane({ id: row.id }).then((r) => {
            this.$notify({
              title: '成功',
              message: '删除成功',
              type: 'success',
              duration: 2000
            })
            this.list.splice(index, 1)
          })
        }).catch(() => {
          this.$notify({
              title: '取消',
              message: '已取消删除',
              type:'info',
              duration: 2000
            })          
        });
    },
    // 重置弹窗
    cancleDialog() {
      this.dialogFormVisible = false
      this.resetTemp()
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
.el-select,.el-input {
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
.svg-icon {
    font-size:1.8em;
}
</style>
