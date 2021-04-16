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
      <el-table-column label="创建时间" align="center">
        <template slot-scope="scope">
          <span v-text="showTime(scope.row.createTime)"></span>
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
      <el-table-column label="位置" align="center">
        <template slot-scope="scope">
          <span v-text="showPlace(scope.row.equipPosition)"></span>
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
              :key="item.baseValue"
                  :label="item.baseKey"
                  :value="Number(item.baseValue)"
            />
          </el-select>
        </el-form-item> 
         <el-form-item label="位置" prop="equipPosition">
          <el-select
            v-model="temp.equipPosition"
            placeholder="请选择"
            clearable
          >
            <el-option
              v-for="item in positionOption"
              :key="item.baseValue"
                  :label="item.baseKey"
                  :value="item.baseValue"
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
        equipName:null,
        equipPosition:null,
        equipType:null
      },
      temp: {
        id: null,
        equipPosition: null,
        equipName: null,
        equipNum:null,
        equipType:null,
        createTime:null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        equipPosition: [
          { required: true, message: '请选择位置', trigger: 'change' }
        ],
        // equipPosition: [
        //   { required: true, message: '位置不能为空', trigger: 'blur' }
        // ],
        equipName: [
          { required: true, message: '设备名称不能为空', trigger: 'blur' }
        ],
        equipNum: [
          { required: true, message: '设备编号不能为空', trigger: 'blur' }
        ],
        equipType: [
          { required: true, message: '请选择设备类型', trigger: 'change' }
        ]
      },
      positionOption: [],
      typeOption:[],
      page:1
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getArea().then(data => {
      this.positionOption = data
    })
    this.getEquipType().then(data => {
      this.typeOption = data
    })
  },
  methods: {
    // getTime(){
    //   let date = new Date()
    //   this.showTime = 
    //     date.getFullYear()+'-'
    //   +((date.getMonth()+1).toString().length<2?'0'+(date.getMonth()+1):(date.getMonth()+1))+'-'
    //   +(date.getDate().toString().length<2?'0'+date.getDate():date.getDate())+' '
    //   +((date.getHours()+1).toString().length<2?'0'+(date.getHours()+1):(date.getHours()+1))+':'
    //   +((date.getMinutes()+1).toString().length<2?'0'+(date.getMinutes()+1):(date.getMinutes()+1))+':'
    //   +((date.getSeconds()+1).toString().length<2?'0'+(date.getSeconds()+1):(date.getSeconds()+1))
    //   return this.showTime
    // },
    showTime(val){
        if(val){
          return parseTime(val, '{y}-{m}-{d} {h}:{i}:{s}') 
        }
    },
    showPlace(val){
      for(let i=0;i<this.positionOption.length;i++){
        if (parseInt(this.positionOption[i].baseValue) === parseInt(val)) {
          return this.positionOption[i].baseKey
        }
      }
    },
    showType(val){
      for(let i=0;i<this.typeOption.length;i++){
        if (parseInt(this.typeOption[i].baseValue) === parseInt(val)) {
          return this.typeOption[i].baseKey
        }
      }
    },
    getList(val) {
      if(val){
        this.page = val.page
      }
      this.listLoading = true
      info.equipList(this.listQuery).then((r) => {
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
        equipPosition: null,
        equipName: null,
        equipNum:null,
        equipType:null,
        createTime:null
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
          info.addEquip(this.temp).then((r) => {
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
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    updateData() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          info.modifyEquip(this.temp).then((r) => {
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
          info.deleteEquip({ id: row.id }).then((r) => {
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
