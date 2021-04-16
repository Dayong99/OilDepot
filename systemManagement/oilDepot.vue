<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.oilDepotName" 
      placeholder="油库名称" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-input 
      v-model="listQuery.oilCode" 
      placeholder="单位代码" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
          <el-select 
          v-model="listQuery.depotLevel" 
          class="filter-item" 
          placeholder="请选择" 
          @clear="handleFilter"
          clearable >
            <el-option 
            v-for="(item,index) in levelOption" 
            :key="index" 
            :label="item.label" 
            :value="item.value" />
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
      <el-table-column label="油库名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.oilDepotName }}</span>
        </template>
      </el-table-column>
      <el-table-column label="建造年代" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.buildAge.substring(0,4) }}年</span>
        </template>
      </el-table-column>
      <el-table-column label="单位代码" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.oilCode }}</span>
        </template>
      </el-table-column>
      <el-table-column label="单位名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.departName }}</span>
        </template>
      </el-table-column>
      <el-table-column label="油库等级" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.depotLevel | showLevel }}</span>
        </template>
      </el-table-column>
      <el-table-column label="总容量" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.capacity }}</span>
        </template>
      </el-table-column>
      <el-table-column label="备注" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.remark===null?'暂无':scope.row.remark }}</span>
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

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" width="600px" @close="cancleDialog">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="120px">
        <el-form-item label="油库名称" prop="oilDepotName">
          <el-input v-model="temp.oilDepotName" clearable />
        </el-form-item>
        <el-form-item label="建造年代" prop="buildAge">
          <el-date-picker
            v-model="temp.buildAge"
            type="year"
            placeholder="选择年"
            value-format="yyyy">
            </el-date-picker>
        </el-form-item>
        <el-form-item label="单位代码" prop="oilCode">
          <el-input v-model="temp.oilCode" clearable />
        </el-form-item>
        <el-form-item label="单位名称" prop="departName">
          <el-input v-model="temp.departName" clearable />
        </el-form-item>
        <el-form-item label="油库等级" prop="depotLevel">
           <el-select 
           v-model="temp.depotLevel" 
           style="width: 200px" 
           class="filter-item" 
           placeholder="请选择" 
           clearable >
            <el-option 
            v-for="(item,index) in levelOption" 
            :key="index" 
            :label="item.label" 
            :value="item.value" />
          </el-select>
        </el-form-item>
        <el-form-item label="总容量" prop="capacity">
           <el-input-number v-model="temp.capacity" :min="0"  label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="说明">
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
    showLevel(val) {
      switch (parseInt(val)) {
        case 0 :
          return '一级'
        case 1 :
          return '二级'
        case 2 :
          return '三级'
      }
    }
  },
  data() {
    return {
      list: null,
      total: 0,

      levelOption: [
        {
          label: '一级',
          value: 0
        },
        {
          label: '二级',
          value: 1
        },
        {
          label: '三级',
          value: 2
        }
      ],

      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        oilDepotName: null,
        OilCode: null,
        depotLevel:null
      },
      statusOptions: ['published', 'draft', 'deleted'],
      temp: {
        id: null,
        oilDepotName: null,
        buildAge: null,
        oilCode: null,
        departName: null,
        depotLevel: null,
        capacity: null,
        remark: null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        oilDepotName: [
          { required: true, message: '油库名称不能空', trigger: 'blur' }
        ],
        buildAge: [
          { required: true, message: '请选择建造年代', trigger: 'change' }
        ],
        oilCode: [
          { required: true, message: '单位代码不能空', trigger: 'blur' }
        ],
        departName: [
          { required: true, message: '单位名称不能为空', trigger: 'blur' }
        ],
        depotLevel: [
          { required: true, message: '请选择油库等级', trigger: 'change' }
        ],
        capacity: [
          { required: true, message: '总容量不能为空', trigger: 'blur' }
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
    getList(val) {
      // if (val) {
      //   this.page = val.page
      // }
      // console.log(val)
      this.listLoading = true
      system.getDepot(this.listQuery).then((r) => {
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
        oilDepotName: null,
        buildAge: null,
        oilCode: null,
        departName: null,
        depotLevel: null,
        capacity: null,
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
          this.temp.buildAge = this.temp.buildAge + '-01-01 10:10:10' 
          system.addDepot(this.temp).then((r) => {
            this.temp.id = r.data
            this.list.push(this.temp)
            this.dialogFormVisible = false
            // this.getList()
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
          if(this.temp.buildAge.length===4){
            this.temp.buildAge = this.temp.buildAge + '-01-01 10:10:10' 
          }
          system.modifyDepot(this.temp).then((r) => {
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
          system.deleteDepot({ id: row.id }).then((r) => {
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
</style>
