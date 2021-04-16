<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.typeName" 
      placeholder="名称" 
      style="width: 200px;" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-select
            v-model="listQuery.baseInfo"
            placeholder="报警类型"
            clearable
            @clear="handleFilter"
          >
            <el-option
              v-for="item in typeOption"
              :key="item.baseValue"
                  :label="item.baseKey"
                  :value="Number(item.baseValue)"
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

      <el-table-column label="名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.typeName }}</span>
        </template>
      </el-table-column>

      <el-table-column label="原因" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.typeCause }}</span>
        </template>
      </el-table-column>
      
      <el-table-column label="阙值" align="center" width="100">
        <template slot-scope="scope">
          <span>{{ scope.row.typeValue }}</span>
        </template>
      </el-table-column>
      <el-table-column label="报警设备" align="center">
        <template slot-scope="scope">
          <span v-text="showEquip(scope.row.equipId)"></span>
        </template>
      </el-table-column>

      <el-table-column label="报警类型" align="center">
        <template slot-scope="scope">
          <span v-text="showType(scope.row.baseInfo)"></span>
        </template>
      </el-table-column>
      <el-table-column label="plc对象" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.plcid }}</span>
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
        <el-form-item label="名称" prop="typeName">
          <el-input v-model="temp.typeName" clearable />
        </el-form-item>
        
        <el-form-item label="原因" prop="typeCause">
          <el-input
            type="textarea"
            style="width:100%;"
            v-model="temp.typeCause">
          </el-input>
        </el-form-item>
        <el-form-item label="阙值" prop="typeValve">
          <el-input-number v-model="temp.typeValve" :min="0" label="描述文字"  :step="1" size="small"></el-input-number>
        </el-form-item>
        
        <el-form-item label="报警设备" prop="equipId">
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
        <el-form-item label="报警类型" prop="baseInfo">
          <el-select
            v-model="temp.baseInfo"
            placeholder="请选择"
            clearable
          >
            <el-option
              v-for="item in typeOption"
              :key="item.baseValue"
                  :label="item.baseKey"
                  :value="Number(item.baseValue)"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="plc对象" prop="plcid">
          <el-select
            v-model="temp.plcid"
            placeholder="请选择"
            clearable
          >
            <el-option
              v-for="item in plcOption"
              :key="item.itemId"
                  :label="item.itemId"
                  :value="item.itemId"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="备注">
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
        typeName:null,
        baseInfo:null
      },
      temp: {
        id: null,
        typeName: null,
        typeValve: null,
        typeCause: null,
        equipId:null,
        remark:null,
        baseInfo:null,
        plcid:null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        typeName: [
          { required: true, message: '名称不能空', trigger: 'blur' }
        ],
        typeValve: [
          { required: true, message: '阙值不能为空', trigger: 'change' }
        ],
        typeCause: [
          { required: true, message: '原因不能空', trigger: 'blur' }
        ],
        equipId: [
          { required: true, message: '请选择报警设备', trigger: 'change' }
        ],
        baseInfo: [
          { required: true, message: '请选择报警类型', trigger: 'change' }
        ],
        plcid: [
          { required: true, message: '请选择plc对象', trigger: 'change' }
        ]
      },
      typeOption: [],
      equipOption:[],
      plcOption:[]
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getAlarmType().then(data => {
      this.typeOption = data
    })
    this.getAlarmEquip().then(data => {
      this.equipOption = data
    })
    this.getPlc().then(data => {
      this.plcOption = data
    })
  },
  methods: {
    showType(val){
      for(let i=0;i<this.typeOption.length;i++){
        if (parseInt(this.typeOption[i].baseValue) === parseInt(val)) {
          return this.typeOption[i].baseKey
        }
      }
    },
    showEquip(val){
      for(let i=0;i<this.equipOption.length;i++){
        if (parseInt(this.equipOption[i].id) === parseInt(val)) {
          return this.equipOption[i].equipName
        }
      }
    },
    getList(val) {
      this.listLoading = true
      info.typeList(this.listQuery).then((r) => {
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
        typeName: null,
        typeValve: null,
        typeCause: null,
        equipId:null,
        remark:null,
        baseInfo:null
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
          info.addType(this.temp).then((r) => {
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
          info.modifyType(this.temp).then((r) => {
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
          info.deleteType({ id: row.id }).then((r) => {
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
