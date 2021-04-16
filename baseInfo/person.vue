<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.pname" 
      placeholder="人员名称" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-input 
      v-model="listQuery.skill" 
      placeholder="技能" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-select
        v-model="listQuery.isJob"
        placeholder="在岗状态"
        clearable
        style="width:120px;"
        @clear="handleFilter"
      >
        <el-option
          v-for="item in jobOption"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        />
      </el-select>
      <el-select
        v-model="listQuery.officer"
        placeholder="身份"
        clearable
        style="width:120px;"
        @clear="handleFilter"
      >
        <el-option
          v-for="item in officerOption"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        />
      </el-select>
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
      <el-table-column label="人员姓名" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.pname }}</span>
        </template>
      </el-table-column>
      <el-table-column label="生日" align="center">
        <template slot-scope="scope">
          <span v-text="showTime(scope.row.birthday)"></span>
        </template>
      </el-table-column>

      <el-table-column label="退伍日期" align="center">
        <template slot-scope="scope">
          <span v-text="showTime(scope.row.veterantime)"></span>
        </template>
      </el-table-column>
      <el-table-column label="在岗状态" align="center">
        <template slot-scope="scope">
          <span>{{ parseInt(scope.row.isJob)===0?'在岗':'退休' }}</span>
        </template>
      </el-table-column>
      <el-table-column label="技能" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.skill }}</span>
        </template>
      </el-table-column>
      <el-table-column label="身份" align="center">
        <template slot-scope="scope">
          <span>{{ parseInt(scope.row.officer)===0?'军官':'士官' }}</span>
        </template>
      </el-table-column>
      <el-table-column label="所属油料股" align="center">
        <template slot-scope="scope">
          <span v-text="showDepot(scope.row.oilDepotId)"></span>
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

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" @close="cancleDialog" width="700px">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="100px" >
        <el-form-item label="人员姓名" prop="pname">
          <el-input v-model="temp.pname" clearable />
        </el-form-item>
        <el-form-item label="生日">
          <el-date-picker
            v-model="temp.birthday"
            type="date"
            placeholder="选择日期"
            value-format="yyyy-MM-dd"
            clearable>
            </el-date-picker>
        </el-form-item>
        <el-form-item label="退伍日期">
          <el-date-picker
            v-model="temp.veterantime"
            type="date"
            placeholder="选择日期"
            value-format="yyyy-MM-dd"
            clearable>
            </el-date-picker>
        </el-form-item>
        <el-form-item label="在岗状态" prop="isJob">
          <el-radio-group v-model="temp.isJob">
            <el-radio label="0">在岗</el-radio>
            <el-radio label="1">退休</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="技能" prop="skill">
          <el-input v-model="temp.skill" clearable />
        </el-form-item>
        <el-form-item label="身份">
          <el-radio-group v-model="temp.officer">
            <el-radio label="0">军官</el-radio>
            <el-radio label="1">士官</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="所属油料股" prop="oilDepotId">
          <el-select
            v-model="temp.oilDepotId"
            placeholder="请选择"
            clearable
          >
            <el-option
              v-for="item in depotOption"
              :key="item.id"
              :label="item.oilDepotName"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogStatus==='create'?createData():updateData()" >
          确认
        </el-button>
        <el-button @click="cancleDialog" >
          取消
        </el-button>
      </div>
    </el-dialog>
  </div>
</template>

<script>
import info from '@/api/baseInfo'
import Pagination from '@/components/Pagination'
import { parseTime } from '@/utils'

export default {
  components: { Pagination },
  filters: {
    statusFilter(status) {
      const statusMap = {
        published: 'success',
        draft: 'system',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
        jobOption:[
            {
                label:'在岗',
                value:0
            },
            {
                label:'离岗',
                value:1
            }
        ],
        officerOption:[
            {
                label:'军官',
                value:0
            },
            {
                label:'士官',
                value:1
            }
        ],
      depotOption:[],
      list: null,
      total: 0,

      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        pname:null,
        skill:null,
        isJob:null,
        officer:null
      },
      temp: {
        id: null,
        pname: null,
        birthday: null,
        veterantime: null,
        isJob: '0',
        skill: null,
        officer: '0',
        oilDepotId: null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        pname: [
          { required: true, message: '人员名称不可为空', trigger: 'blur' }
        ],
        isJob: [
          { required: true, message: '请选择在岗状态', trigger: 'change' }
        ],
        skill: [
          { required: true, message: '技能不能为空', trigger: 'blur' }
        ],
        oilDepotId: [
          { required: true, message: '请选择所属油料股', trigger: 'change' }
        ]
      }
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getOilDepot().then(data => {
      this.depotOption = data
    })
  },
  methods: {
    showDepot(val){
      for(let i=0;i<this.depotOption.length;i++){
        if (this.depotOption[i].id === parseInt(val)) {
          return this.depotOption[i].oilDepotName
        }
      }
    },
    showTime(val){
        if(val){
            return parseTime(val, '{y}-{m}-{d}') 
        }
    },
    getList() {
      this.listLoading = true
      info.personList(this.listQuery).then((r) => {
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
        pname: null,
        birthday: null,
        veterantime: null,
        isJob: '0',
        skill: null,
        officer: '0',
        oilDepotId: null
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
          this.temp.birthday += ' 00:00:00'
          this.temp.veterantime += ' 00:00:00'
          info.addPerson(this.temp).then((r) => {
             this.dialogFormVisible = false
             this.temp.id = r.data
              this.list.push(this.temp)
              this.$notify({
                title: '成功',
                message: '创建成功',
                type: 'success',
                duration: 2000
              })
              //this.getList()
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
      this.temp.isJob = this.temp.isJob.toString()
      this.temp.officer = this.temp.isJob.toString()
      this.temp.oilDepotId = Number(this.temp.oilDepotId)
      this.temp.oilDepot
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    updateData() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          info.modifyPerson(this.temp).then((r) => {
            for (const v of this.list) {
              if (v.id === this.temp.id) {
                const index = this.list.indexOf(v)
                this.temp.oilType = Number(this.temp.oilType)
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
          info.deletePerson({ id: row.id }).then((r) => {
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
    // 关闭弹窗
    closeUpload() {
      this.oilVisible = false
    },
    // 重置弹窗
    cancleDialog() {
      this.dialogFormVisible = false
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
.el-input,.el-select {
  width:200px;
}
</style>
