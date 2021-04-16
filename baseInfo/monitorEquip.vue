<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.equipName" 
      placeholder="设备名称" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <!-- <el-input 
      v-model="listQuery.equipNum" 
      placeholder="设备编号" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" /> -->
      <el-select
            v-model="listQuery.equipType"
            placeholder="设备类型"
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
      <el-select
        v-model="listQuery.equipPosition"
        placeholder="位置"
        clearable
        @clear="handleFilter"
      >
        <el-option
          v-for="item in positionOption"
          :key="item.baseValue"
                  :label="item.baseKey"
                  :value="item.baseValue"
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
      <el-table-column label="设备名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.equipName }}</span>
        </template>
      </el-table-column>
      <el-table-column label="设备编号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.equipNum }}</span>
        </template>
      </el-table-column>
      <el-table-column label="设备类型" align="center">
        <template slot-scope="scope">
          <span v-text="showType(scope.row.equipType)"></span>
        </template>
      </el-table-column>
      <el-table-column label="设备位置" align="center">
        <template slot-scope="scope">
          <span v-text="showPlace(scope.row.equipPosition)"></span>
        </template>
      </el-table-column>
      <el-table-column label="创建日期" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.createtime }}</span>
        </template>
      </el-table-column>
      <el-table-column label="保养周期" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.maintenance }}</span>
        </template>
      </el-table-column>
      <el-table-column label="上次保养日" align="center">
        <template slot-scope="scope">
          <span v-text="showTime(scope.row.maintenanceday)"></span>
        </template>
      </el-table-column>
      <el-table-column label="在线状态" align="center">
        <template slot-scope="scope">
          <i :class="parseInt(scope.row.isOnline)===0?'el-icon-error noUse':'el-icon-success isUse'" />       
       </template>
      </el-table-column>
            <el-table-column label="说明" align="center">
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

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" @close="cancleDialog" width="700px">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="100px" >
        <el-form-item label="设备名称" prop="equipName">
          <el-input v-model="temp.equipName" clearable />
        </el-form-item>
        <el-form-item label="设备编号" prop="equipNum">
          <el-input v-model="temp.equipNum" clearable />
        </el-form-item> 
        <el-form-item label="设备类型" prop="equipType">
          <el-select
            v-model="temp.equipType"
            placeholder="请选择"
            clearable
          >
            <el-option
              v-for="item in typeOption"
              :key="Number(item.baseValue)"
              :label="item.baseKey"
              :value="Number(item.baseValue)"
            />
          </el-select>
        </el-form-item>

        <!-- <el-form-item label="创建日期" prop="createtime">
                <el-date-picker
                v-model="temp.createtime"
                type="date"
                value-format="yyyy-MM-dd"
                placeholder="选择日期">
                </el-date-picker>
            </el-form-item> -->
        <el-form-item label="设备位置" prop="equipPosition">
          <el-select
            v-model="temp.equipPosition"
            placeholder="请选择"
            clearable
          >
            <el-option
              v-for="item in positionOption"
              :key="item.baseValue"
                  :label="item.baseKey"
                  :value="Number(item.baseValue)"
            />
          </el-select>
        </el-form-item> 
        <el-form-item label="保养周期" prop="maintenance">
          <el-input v-model.number="temp.maintenance" clearable />
        </el-form-item>
        <el-form-item label="上次保养日" prop="maintenanceday">
                <el-date-picker
                v-model="temp.maintenanceday"
                type="date"
                value-format="yyyy-MM-dd"
                placeholder="选择日期">
                </el-date-picker>
            </el-form-item>
        <el-form-item label="在线状态" prop="isOnline">
          <el-radio-group v-model="temp.isOnline">
            <el-radio label="0">不在线</el-radio>
            <el-radio label="1">在线设备</el-radio>
          </el-radio-group>
        </el-form-item>
        <el-form-item label="说明">
          <el-input
            type="textarea"
            style="width:100%"
            v-model="temp.remark">
          </el-input>
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
        draft: 'info',
        deleted: 'danger'
      }
      return statusMap[status]
    }
  },
  data() {
    return {
      typeOption:[],
      positionOption: [],
      list: null,
      total: 0,

      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        equipName:null,
        equipPosition:null,
        equipType:null
      },
      temp: {
        id: null,
        equipName:null,
        equipType:null,
        equipNum:null,
        remark:null,
        createtime:'2019-12-09 10:10:10',
        maintenance:null,
        maintenanceday:null,
        isOnline:null,
        equipPosition:null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        equipPosition: [
          { required: true, message: '请选择设备位置', trigger: 'change' }
        ],
        equipName: [
          { required: true, message: '设备名称不能为空', trigger: 'blur' }
        ],
        equipType: [
          { required: true, message: '请选择设备类型', trigger: 'change' }
        ],
        equipNum: [
          { required: true, message: '设备编号不能为空', trigger: 'blur' }
        ],
        // createtime: [
        //   { required: true, message: '创建日期不能为空', trigger: 'blur' }
        // ],
        maintenance: [
          { required: true, message: '保养周期不能为空', trigger: 'blur' },
          { type: 'number', message: '保养周期必须为数字值'}
        ],
        maintenanceday: [
          { required: true, message: '上次保养日不能为空', trigger: 'blur' }
        ],
        isOnline: [
          { required: true, message: '请选择在线状态', trigger: 'change' }
        ]
      },
      page:1
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getMonitorEquipType().then(data => {
      this.typeOption = data
    })
    this.getArea().then(data => {
      this.positionOption = data
    })
  },
  methods: {
    showTime(val){
        if(val){
            return parseTime(val, '{y}-{m}-{d}') 
        }
    },
    showType(val){
      for(let i = 0;i<this.typeOption.length;i++){
        if(parseInt(val)===parseInt(this.typeOption[i].baseValue)){
          return this.typeOption[i].baseKey
        }
      }
    },
    showPlace(val){
      for(let i=0;i<this.positionOption.length;i++){
        if (parseInt(this.positionOption[i].baseValue) === parseInt(val)) {
          return this.positionOption[i].baseKey
        }
      }
    },
    getList(val) {
      if(val){
        this.page = val.page
      }
      this.listLoading = true
      info.monitorequipList(this.listQuery).then((r) => {
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
        equipName:null,
        equipType:null,
        equipNum:null,
        remark:null,
        createtime:'2019-12-09 10:10:10',
        maintenance:null,
        maintenanceday:null,
        isOnline:null,
        equipPosition:null
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
          this.temp.maintenanceday += ' 00:00:00'
          console.log(this.temp.maintenanceday)
          info.addMonitorequip(this.temp).then((r) => {
            this.dialogFormVisible = false
            // this.temp.id = r.data
            //   this.list.push(this.temp)
              this.$notify({
                title: '成功',
                message: '创建成功',
                type: 'success',
                duration: 2000
              })
              this.getList()
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
      this.temp.isOnline = this.temp.isOnline.toString()
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    updateData() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          this.temp.maintenanceday += ' 00:00:00'
          info.modifyMonitorequip(this.temp).then((r) => {
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
          info.deleteMonitorequip({ id: row.id }).then((r) => {
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
.isUse {
    color:#13ce66;
}
.noUse {
    color:#ff4949;
}
i {
    font-size:1.5em;
}
</style>
