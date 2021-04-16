<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.tankName" 
      placeholder="罐车名称" 
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
      <el-table-column label="罐车名称" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.tankName }}</span>
        </template>
      </el-table-column>
      <el-table-column label="车辆类别" align="center">
        <template slot-scope="scope">
          <span v-text="showType(scope.row.carType)"></span>
        </template>
      </el-table-column>
      <el-table-column label="油品" align="center">
        <template slot-scope="scope">
          <span v-text="showOil(scope.row.oilType)"></span>
        </template>
      </el-table-column>
      <el-table-column label="密度" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.oilDensity }}</span>
        </template>
      </el-table-column>
      <el-table-column label="体积 (L)" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.oilVolume }}</span>
        </template>
      </el-table-column>
      <el-table-column label="重量 (kg)" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.oilStock }}</span>
        </template>
      </el-table-column>
      <el-table-column label="最大载重量 (kg)" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.maxStock }}</span>
        </template>
      </el-table-column>
      <el-table-column label="品牌" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.brand }}</span>
        </template>
      </el-table-column>
      <el-table-column label="车牌号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.license }}</span>
        </template>
      </el-table-column>
      <el-table-column label="型号" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.model }}</span>
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

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" @close="cancleDialog" width="800px">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="100px" >
        <el-form-item label="罐车名称" prop="tankName">
          <el-input v-model="temp.tankName" clearable />
        </el-form-item>
        <el-form-item label="车辆类别" prop="carType">
          <el-select
            v-model="temp.carType"
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
              :value="Number(item.baseValue)"
            />
          </el-select>
        </el-form-item> 
        <el-row :gutter="24">
          <el-col :span="8">
             <el-form-item label="密度">
              <el-input-number v-model="temp.oilDensity" :min="0"  label="描述文字" size="small"></el-input-number>
            </el-form-item>
          </el-col>
          <el-col :span="8">
            <el-form-item label="体积 (L)">
              <el-input-number v-model="temp.oilVolume" :min="0"  label="描述文字" size="small"></el-input-number>
            </el-form-item> 
          </el-col>
          <el-col :span="8">
            <el-form-item label="重量 (kg)">
              <el-input-number v-model="temp.oilStock" :min="0"  label="描述文字" size="small"></el-input-number>
            </el-form-item>
          </el-col>
        </el-row>
        <el-form-item label="最大载重量" prop="maxStock">
          <el-input-number v-model="temp.maxStock" :min="0"  label="描述文字" size="small"></el-input-number>
        </el-form-item> 
        <el-form-item label="品牌">
          <el-input v-model="temp.brand" clearable />
        </el-form-item>
        <el-form-item label="车牌号">
          <el-input v-model="temp.license" clearable />
        </el-form-item>
        <el-form-item label="型号">
          <el-input v-model="temp.model" clearable />
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
    // showOil(val){
    //   switch(parseInt(val)){
    //     case 0 :
    //       return '汽油'
    //     case 1 :
    //       return '柴油'
    //     case 2 :
    //       return '航空3号喷气燃料油'
    //   }
    // }
  },
  data() {
    return {
      list: null,
      total: 0,

      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        tankName:null
      },
      temp: {
        id: null,
        tankName: null,
        oilType: null,
        oilStock: null,
        oilVolume: null,
        oilDensity: null,
        maxStock: null,
        license: null,
        model: null,
        brand: null,
        carType:null
      },
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        tankName: [
          { required: true, message: '罐车名称不能为空', trigger: 'blur' }
        ],
        oilType: [
          { required: true, message: '请选择油品', trigger: 'blur' }
        ],
        maxStock: [
          { required: true, message: '最大载重量不能为空', trigger: 'change' }
        ],
        carType: [
          { required: true, message: '请选择车辆类别', trigger: 'change' }
        ]
      },
      page:1,
      oilOption:[
        {
          label:'汽油',
          value:0
        },
        {
          label:'柴油',
          value:1
        },
        {
          label:'3号喷气燃料',
          value:2
        }
      ],
      typeOption:[]
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getCarType().then(data => {
      this.typeOption = data
    })
    this.getOil().then(data => {
      this.oilOption = data
    })
  },
  methods: {
     showOil(val){
      for(let i = 0;i<this.oilOption.length;i++){
        if(parseInt(val)===parseInt(this.oilOption[i].baseValue)){
          return this.oilOption[i].baseKey
        }
      }
    },
     showType(val){
      for(let i = 0;i<this.typeOption.length;i++){
        if(parseInt(val)===parseInt(this.typeOption[i].baseValue)){
          return this.typeOption[i].baseKey
        }
      }
    },
    getList(val) {
      if(val){
        this.page = val.page
      }
      this.listLoading = true
      info.carList(this.listQuery).then((r) => {
        if (r.code === 20000) {
          this.list = r.data.records
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
        tankName: null,
        oilType: null,
        oilStock: null,
        oilVolume: null,
        oilDensity: null,
        maxStock: null,
        license: null,
        model: null,
        brand: null,
        carType:null
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
          info.addCar(this.temp).then((r) => {
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
          info.modifyCar(this.temp).then((r) => {
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
          info.deleteCar({ id: row.id }).then((r) => {
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
