<template>
  <div class="app-container">
    <div class="filter-container">
       <el-select
            v-model="listQuery.equipId"
            placeholder="监控设备"
            clearable
            @clear="handleFilter"
          >
            <el-option
              v-for="item in equipOption"
              :key="item.id"
              :label="item.equipName"
              :value="item.id"
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
      <!-- <el-table-column label="编号" align="center" width="80">
        <template slot-scope="scope">
          <span>{{ scope.row.id }}</span>
        </template>
      </el-table-column> -->
      <el-table-column label="编号" align="center" width="80">
        <template slot-scope="scope">
          <span>{{ scope.$index+1 }}</span>
        </template>
      </el-table-column>
      <el-table-column label="设备" align="center">
        <template slot-scope="scope">
          <span v-text="showEquip(scope.row.equipId)"></span>
        </template>
      </el-table-column>
      <el-table-column label="维修部件" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.repairpart }}</span>
        </template>
      </el-table-column>

      <el-table-column label="维修时间" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.repairtime }}</span>
        </template>
      </el-table-column>
      <el-table-column label="维修内容" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.repaircontent }}</span>
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
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="85px" >
        <el-form-item label="设备" prop="equipId">
          <el-select
            v-model="temp.equipId"
            placeholder="请选择"
            clearable
          >
            <el-option
              v-for="item in equipOption"
              :key="item.id"
              :label="item.equipName"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="维修部件" prop="repairpart">
          <el-input
            clearable
            v-model="temp.repairpart">
          </el-input>
        </el-form-item>
        <el-form-item label="维修时间" prop="repairtime">
          <el-date-picker
            v-model="temp.repairtime"
            type="datetime"
            value-format="yyyy-MM-dd hh:mm:ss"
            placeholder="选择日期时间">
          </el-date-picker>
        </el-form-item>
        <el-form-item label="维修内容" prop="repaircontent">
          <el-input
            type="textarea"
            v-model="temp.repaircontent">
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
  </div>
</template>

<script>
import online from '@/api/onlineEquip'
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
        equipId:null
      },
      temp: {
        id: null,
        repaircontent: null,
        repairtime: null,
        equipId: null,
        repairpart:null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        repairtime: [
          { required: true, message: '维修时间不能为空', trigger: 'blur' }
        ],
        repairpart: [
          { required: true, message: '维修部件不能为空', trigger: 'blur' }
        ],
        equipId: [
          { required: true, message: '请选择设备', trigger: 'change' }
        ],
        repaircontent: [
          { required: true, message: '维修内容不能为空', trigger: 'blur' }
        ]
      },
      itemOption: [],
      equipOption:[]
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getMonitorType().then(data => {
      this.itemOption = data
    })
    this.getEquip().then(data => {
      this.equipOption = data
    })
  },
  methods: {
    showItem(val){
      for(let i = 0;i<this.itemOption.length;i++){
        if(parseInt(val)===parseInt(this.itemOption[i].baseValue)){
          return this.itemOption[i].baseKey
        }
      }
    },
    showEquip(val){
      for(let i = 0;i<this.equipOption.length;i++){
        if(parseInt(val)===parseInt(this.equipOption[i].id)){
          return this.equipOption[i].equipName
        }
      }
    },
    getList(val) {
      this.listLoading = true
      online.maintainRecord(this.listQuery).then((r) => {
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
        repaircontent: null,
        repairtime: null,
        equipId: null,
        repairpart:null
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
          online.addMaintain(this.temp).then((r) => {
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
          online.modifyMaintain(this.temp).then((r) => {
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
          online.deleteMaintain({ id: row.id }).then((r) => {
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
