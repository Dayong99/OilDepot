<template>
  <div class="app-container">
    <div class="filter-container">
      <el-select
        v-model="listQuery.baseType"
        placeholder="类型"
        clearable
        @clear="handleFilter"
      >
        <el-option
          v-for="item in typeOption"
          :key="item.id"
          :label="item.typename"
          :value="item.id"
        />
      </el-select>
      <el-input 
      v-model="listQuery.baseName" 
      placeholder="名称" 
      style="width: 200px;" 
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
      <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="typeManagement">
        类型管理
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
      <el-table-column label="名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.baseKey }}</span>
        </template>
      </el-table-column>
      <el-table-column label="类型" align="center">
        <template slot-scope="scope">
          <span v-text="showType(scope.row.baseType)"></span>
        </template>
      </el-table-column>

      <!-- <el-table-column label="类型名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.baseName }}</span>
        </template>
      </el-table-column> -->

      <el-table-column label="值" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.baseValue }}</span>
        </template>
      </el-table-column>
      <el-table-column label="备注" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.remark }}</span>
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

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" @close="cancleDialog" width="600px">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="80px" >
        <el-form-item label="名称" prop="baseKey">
          <el-input v-model="temp.baseKey" clearable  />
        </el-form-item>
        <el-form-item label="类型" prop="baseType">
          <el-select
            v-model="temp.baseType"
            placeholder="请选择" 
            clearable
            @change="showTypeName(temp.baseType)"
            @clear="clearTypeName"
          >
            <el-option
              v-for="item in typeOption"
              :key="item.id"
              :label="item.typename"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="类型名称">
          <span v-model="temp.baseName" style="padding-left:15px;">{{temp.baseName}}</span>
        </el-form-item>
        <el-form-item label="值" prop="baseValue">
          <el-input v-model="temp.baseValue" clearable />
        </el-form-item>
        <el-form-item label="备注" prop="remark">
          <el-input
            type="textarea"
            style="width:100%;"
            v-model="temp.remark">
          </el-input>
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

    <el-dialog title="类型管理" :visible.sync="typeVisible" @close="cancleType">
      <el-dialog
        :visible.sync="innerVisible"
        :title="textMap[typeStatus]"
        width="550px"
        append-to-body
      >
        <el-form
          ref="typeForm"
          :rules="typeRules"
          :model="type"
          label-position="right"
          label-width="110px"
          style="width: 400px; margin-left:50px;"
        >
          <el-form-item label="类型名称" prop="typename">
            <el-input v-model="type.typename" clearable />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="typeStatus==='create'?createType():updateType()">
            确认
          </el-button>
          <el-button @click="cancleType">
            取消
          </el-button>
        </div>
      </el-dialog>
      <div class="filter-container">
        <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="typeCreate">
          添加
        </el-button>
      </div>

      <el-table
        :data="tableData"
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
        <el-table-column label="类型名称" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.typename }}</span>
          </template>
        </el-table-column>
        <el-table-column label="操作" align="center" width="200">
          <template slot-scope="{row}">
            <el-button type="primary" size="mini" @click="typeUpdate(row)">
              编辑
            </el-button>
            <el-button size="mini" type="danger" @click="typeDelete(row,'deleted')">
              删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
      <pagination v-show="typeTotal>0" :total="typeTotal" :page.sync="typeListQuery.pageNum" :limit.sync="typeListQuery.pageSize" @pagination="getTypeList" />
    </el-dialog>

  </div>
</template>

<script>
import info from '@/api/baseInfo'
import Pagination from '@/components/Pagination'

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

      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        baseType:null,
        baseKey:null
      },
      temp: {
        id: null,
        baseType: null,
        baseName: null,
        baseKey: null,
        baseValue: null,
        remark: null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        baseType: [
          { required: true, message: '请选择类型', trigger: 'change' }
        ],
        baseKey: [
          { required: true, message: '名称不能空', trigger: 'blur' }
        ],
        baseValue: [
          { required: true, message: '值不能为空', trigger: 'blur' }
        ]
      },
      typeOption: [],

      // 类型
      typeVisible: false,
      type: {
        id: null,
        typename: null
      },
      typeRules: {
        typename: [
          { required: true, message: '类型名称不能为空', trigger: 'blur' }
        ]
      },
      typeStatus: '',
      tableData: [],
      innerVisible: false,
      typeListQuery: {
        pageSize: 10,
        pageNum: 1
      },
      typeTotal:0
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getType()
  },
  methods: {
    getType(){
      info.allType().then((r) => {
        if (r.code === 20000) {
          this.typeOption = r.data
        }
      }).catch(() => {
        setTimeout(() => {
            this.$message({
              message: '获取所有类型信息失败',
              type: 'error'
            })
          }, 500)
      })
    },
    showType(val){
      let name = ''
      for (let i = 0; i < this.typeOption.length; i++) {
        if (val === this.typeOption[i].id) {
          name = this.typeOption[i].typename
          break
        }
      }
      return name
    },
    showTypeName(val){
      this.typeOption.forEach((item,index)=>{
        if(item.id === val){
          this.temp.baseName = item.typename
        }
      })
    },
    clearTypeName(){
      this.temp.baseName = null
    },
    getList(val) {
      this.listLoading = true
      info.infoList(this.listQuery).then((r) => {
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
        baseType: null,
        baseName: null,
        baseKey: null,
        baseValue: null,
        remark: null
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
          info.addInfo(this.temp).then((r) => {
            this.dialogFormVisible = false
            this.temp.id = r.data
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
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    updateData() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          info.modifyInfo(this.temp).then((r) => {
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
          info.deleteInfo({ id: row.id }).then((r) => {
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
    },

    // 类型
    getTypeList() {
      info.getInfoType(this.typeListQuery).then((r) => {
        if (r.code === 20000) {
          this.tableData = r.data.rows
          this.typeTotal = r.data.total
        }
      }).catch(() => {
        this.$message.error('获取类型信息失败')
      })
    },
    typeManagement() {
      this.typeVisible = true
      this.getTypeList()
    },

    // 新建
    resetType() {
      this.type = {
        id: null,
        typename: null
      }
    },
    typeCreate() {
      this.resetType()
      this.innerVisible = true
      this.typeStatus = 'create'
      this.$nextTick(() => {
        this.$refs['typeForm'].clearValidate()
      })
    },
    createType() {
      this.$refs['typeForm'].validate((valid) => {
        if (valid) {
          info.addInfoType(this.type).then((r) => {
            this.innerVisible = false
            if (r.code === 20000) {
              this.type.id = r.data
              this.tableData.push(this.type)
              this.getType()
              this.$notify({
                title: '成功',
                message: '创建成功',
                type: 'success',
                duration: 2000
              })
            }
          }).catch(e => {
            this.$notify({
              title: '失败',
              message: '添加失败',
              type: 'error',
              duration: 2000
            })
          })
        }
      })
    },

    // 更新
    typeUpdate(row) {
      this.type = Object.assign({}, row) // copy obj
      this.typeStatus = 'update'
      this.innerVisible = true
      this.$nextTick(() => {
        this.$refs['typeForm'].clearValidate()
      })
    },
    updateType() {
      this.$refs['typeForm'].validate((valid) => {
        if (valid) {
          info.modifyInfoType(this.type).then((r) => {
            for (const v of this.tableData) {
              if (v.id === this.type.id) {
                const index = this.tableData.indexOf(v)
                this.tableData.splice(index, 1, this.type)
                this.getList()
                this.getType()
                break
              }
            }
            this.innerVisible = false
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
    typeDelete(row, status) {
      this.deleteType(row)
    },
    deleteType(row) {
      this.$confirm('此操作将永久删除该条数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning',
          center: true
        }).then(() => {
          const index = this.tableData.indexOf(row)
          info.deleteInfoType({ id: row.id }).then((r) => {
            this.getList()
            this.getType()
            this.$notify({
              title: '成功',
              message: '删除成功',
              type: 'success',
              duration: 2000
            })
            this.tableData.splice(index, 1)
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
    cancleType() {
      this.innerVisible = false
      this.type = {
        name: null,
        id: null,
        remark: null
      }
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
