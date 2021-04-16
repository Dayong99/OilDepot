<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.tankName" 
      placeholder="油罐名称" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-select
        v-model="listQuery.oilType"
        placeholder="油品"
        clearable
        @clear="handleFilter"
      >
        <el-option
          v-for="item in oilOption"
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
      <el-table-column label="油罐编号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.oilNum }}</span>
        </template>
      </el-table-column>
      <el-table-column label="油罐名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.tankName }}</span>
        </template>
      </el-table-column>

      <el-table-column label="油品" align="center">
        <template slot-scope="scope">
          <span v-text="showOil(scope.row.oilType)"></span>
        </template>
      </el-table-column>
      <!-- <el-table-column label="油面高度" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.oilHeight  }}</span>
        </template>
      </el-table-column>
      <el-table-column label="重量" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.oilStock  }}</span>
        </template>
      </el-table-column>
      <el-table-column label="密度" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.oilDensity  }}</span>
        </template>
      </el-table-column>
      <el-table-column label="罐区域" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.areaId  }}</span>
        </template>
      </el-table-column> -->
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

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" @close="cancleDialog">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="90px" >
        <el-form-item label="油罐编号" prop="oilNum">
          <el-input v-model="temp.oilNum" clearable />
        </el-form-item>
        <el-form-item label="油罐名称" prop="tankName">
          <el-input v-model="temp.tankName" clearable />
        </el-form-item>
        <el-form-item label="油品" prop="oilType">
          <el-select
            v-model="temp.oilType"
            placeholder="请选择"    
            clearable
          >
            <el-option
              v-for="item in oilOption"
              :key="item.baseValue"
              :label="item.baseKey"
              :value="item.baseValue"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="油面高度" prop="oilHeight">
          <el-input-number v-model="temp.oilHeight" :min="0"  label="描述文字" ></el-input-number>
        </el-form-item>
        <el-form-item label="重量" prop="oilStock">
          <el-input-number v-model="temp.oilStock" :min="0"  label="描述文字" ></el-input-number>
        </el-form-item>
        <el-form-item label="密度" prop="oilDensity">
          <el-input-number v-model="temp.oilDensity" :min="0"  label="描述文字" ></el-input-number>
        </el-form-item>
        <el-form-item label="罐区域" prop="areaId">
          <el-select
            v-model="temp.areaId"
            placeholder="请选择"
            clearable
          >
            <el-option
              v-for="item in positionOption"
              :key="item.value"
              :label="item.label"
              :value="item.value"
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
      oilOption:[],
      list: null,
      total: 0,

      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        oilType:null,
        tankName:null
      },
      temp: {
        id: null,
        tankName: null,
        oilType: null,
        oilHeight: null,
        oilStock: null,
        oilDensity: null,
        areaId: null,
        oilNum: null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        oilNum: [
          { required: true, message: '油罐编号', trigger: 'blur' }
        ],
        oilType: [
          { required: true, message: '请选择油品', trigger: 'change' }
        ],
        tankName: [
          { required: true, message: '油罐名称不能为空', trigger: 'blur' }
        ]
        // oilHeight: [
        //   { required: true, message: '油面高度不能为空', trigger: 'change' }
        // ],
        // oilStock: [
        //   { required: true, message: '重量不能为空', trigger: 'change' }
        // ],
        // oilDensity: [
        //   { required: true, message: '密度不能为空', trigger: 'change' }
        // ],
        // areaId: [
        //   { required: true, message: '请选择罐区域', trigger: 'change' }
        // ]
      },
      positionOption: [],
      page:1,
      len:null
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getOil().then(data => {
      this.oilOption = data
    })
  },
  methods: {
    showOil(val){
      for(let i=0;i<this.oilOption.length;i++){
        if (parseInt(this.oilOption[i].baseValue) === val) {
          return this.oilOption[i].baseKey
        }
      }
    },
    getList(val) {
      if(val){
        this.page = val.page
      }
      this.listLoading = true
      info.tankList(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.list = r.data.rows
          this.total = r.data.total
          this.listLoading = false
          this.len = this.list.length
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
        tankName: null,
        oilType: null,
        oilHeight: null,
        oilStock: null,
        oilDensity: null,
        areaId: null,
        oilNum: null
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
          info.addTank(this.temp).then((r) => {
             this.dialogFormVisible = false
             this.temp.id = r.data
            this.temp.oilType = Number(this.temp.oilType)
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
      this.temp.oilType = this.temp.oilType.toString()
      this.dialogStatus = 'update'
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    updateData() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          info.modifyTank(this.temp).then((r) => {
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
          info.deleteTank({ id: row.id }).then((r) => {
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
