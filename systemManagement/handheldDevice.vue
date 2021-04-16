<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.name" 
      placeholder="设备名称" 
      style="width: 200px;" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-input 
      v-model="listQuery.number" 
      placeholder="绑定北斗短号"
      style="width: 200px;" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-select 
      v-model="listQuery.type" 
      placeholder="手持端类型" 
      style="width: 200px;"
      @clear="handleFilter" 
      clearable>
        <el-option
          v-for="oitem in typeOptions"
          :key="oitem.value"
          :label="oitem.label"
          :value="oitem.value"
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
      <el-table-column label="创建时间" align="center">
        <template slot-scope="scope">
          <span v-text="showTime(scope.row.createtime)"></span>
        </template>
      </el-table-column>
      <el-table-column label="设备名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.name }}</span>
        </template>
      </el-table-column>
      <el-table-column label="绑定北斗短号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.number }}</span>
        </template>
      </el-table-column>
      <el-table-column label="手持端类型" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.type | showType }}</span>
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

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" width="560px" @close="cancleDialog">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="120px">
        <el-form-item label="设备名称" prop="name">
          <el-input v-model="temp.name" clearable />
        </el-form-item>
        <el-form-item label="绑定北斗短号" prop="number">
          <el-input v-model="temp.number" clearable />
        </el-form-item>
        <el-form-item label="设备类型" prop="type">
           <el-select 
           v-model="temp.type" 
           style="width: 200px" 
           class="filter-item" 
           placeholder="请选择" 
           clearable >
            <el-option 
            v-for="(item,index) in typeOptions" 
            :key="index" 
            :label="item.label" 
            :value="item.value" />
          </el-select>
        </el-form-item>
        <el-form-item label="备注">
          <el-input
            v-model="temp.remark"
            type="textarea"
            placeholder="请输入内容..."
            show-word-limit
            :autosize="{ minRows: 3, maxRows: 6}"
            maxlength="45"
            style="width:400px;"
          />
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
    },
    showType(val) {
      switch (parseInt(val)) {
        case 0 :
          return '机务端'
        case 1 :
          return '附油车服务端'
      }
    }
  },
  data() {
    return {
      list: null,
      total: 0,

      typeOptions: [
        {
          label: '机务端',
          value: 0
        },
        {
          label: '附油车服务端',
          value: 1
        }
      ],

      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        name: null,
        type: null
      },
      statusOptions: ['published', 'draft', 'deleted'],
      temp: {
        id: null,
        name: null,
        type: null,
        number: null,
        remark: null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        name: [
          { required: true, message: '设备名称不能空', trigger: 'blur' }
        ],
        type: [
          { required: true, message: '请选择设备类型', trigger: 'change' }
        ],
        number: [
          { required: true, message: '绑定北斗短号不能空', trigger: 'blur' }
        ]
      },
      page:1
    }
  },
  computed: {
  },
  created() {
    this.getList()
  },
  methods: {
    showTime(val){
      return parseTime(val, '{y}-{m}-{d} {h}:{i}:{s}') 
    },
    getList(val) {
      if (val) {
        this.page = val.page
      }
      console.log(val)
      this.listLoading = true
      system.getDevice(this.listQuery).then((r) => {
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
        name: null,
        type: null,
        number: null,
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
          system.addDevice(this.temp).then((r) => {
            this.dialogFormVisible = false
            this.getList()
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
      delete this.temp.createtime
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          system.modifyDevice(this.temp).then((r) => {
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
          system.deleteDevice({ id: row.id }).then((r) => {
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
.el-form .el-input {
    width:200px;
}
</style>
