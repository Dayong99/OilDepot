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
        placeholder="油料品种"
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

      <el-table-column label="油料品种" align="center">
        <template slot-scope="scope">
          <span v-text="showOil(scope.row.oilType)"></span>
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center" width="300" class-name="small-padding fixed-width">
        <template slot-scope="{row}">
          <el-button type="primary" size="mini" @click="showDetail(row)" plain>
            详情
          </el-button>
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

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible" @close="cancleDialog" width="750px">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="130px" >
        <el-row :gutter="24">
          <el-col :span="12">
            <el-form-item label="油罐名称" prop="tankName">
              <el-input v-model="temp.tankName" clearable />
            </el-form-item>
            <el-form-item label="油罐编号" prop="oilNum">
              <el-input v-model="temp.oilNum" clearable />
            </el-form-item>
            <el-form-item label="罐区域" prop="areaId">
              <el-select
                v-model="temp.areaId"
                placeholder="请选择" 
                clearable
              >
                <el-option
                  v-for="item in areaOption"
                  :key="parseInt(item.baseValue)"
                  :label="item.baseKey"
                  :value="parseInt(item.baseValue)"
                />
              </el-select>
            </el-form-item>
            <el-form-item label="建筑形式" prop="buildingStyle">
              <el-select
                v-model="temp.buildingStyle"
                placeholder="请选择"
                clearable
              >
                <el-option
                  v-for="item in buildOption"
                  :key="parseInt(item.baseValue)"
                  :label="item.baseKey"
                  :value="parseInt(item.baseValue)"
                />
              </el-select>
            </el-form-item>
             <el-form-item label="油料品种" prop="oilType">
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
            <el-form-item label="油面高度 (m)" prop="oilHeight">
              <el-input-number v-model="temp.oilHeight" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>
            <el-form-item label="重量 (kg)" prop="oilStock">
              <el-input-number v-model="temp.oilStock" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>
          </el-col>
          <el-col :span="12">
            <el-form-item label="密度" prop="oilDensity">
              <el-input-number v-model="temp.oilDensity" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>
            <el-form-item label="公称容量 (L)" prop="nominalCapacity">
              <el-input-number v-model="temp.nominalCapacity" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>
            <el-form-item label="实际容量 (L)" prop="actualCapacity">
              <el-input-number v-model="temp.actualCapacity" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>
            <el-form-item label="最大装油高度 (m)" prop="maxHeight">
              <el-input-number v-model="temp.maxHeight" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>     
            <el-form-item label="安全容量 (L)" prop="safeCapacity">
              <el-input-number v-model="temp.safeCapacity" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>
            <el-form-item label="安全高度 (m)" prop="safeHeight">
              <el-input-number v-model="temp.safeHeight" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>
            <el-form-item label="静态锁定值" prop="staticLockValue">
              <el-input-number v-model="temp.staticLockValue" :min="0"  label="描述文字" ></el-input-number>
            </el-form-item>   
          </el-col>
        </el-row>
        <el-form-item label="备注">
          <el-input
            v-model="temp.remark"
            type="textarea"
            placeholder="请输入内容..."
            show-word-limit
            :autosize="{ minRows: 3, maxRows: 6}"
            maxlength="45"
            style="width:100%;"
          />
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

    <el-dialog title="油罐信息详情" :visible.sync="tableVisible" width="1000px">
      <el-row class="info">
        <el-col :span="6">油罐名称：<span>{{ info.tankName }}</span></el-col>
        <el-col :span="6">油罐编号：<span>{{ info.oilNum }}</span></el-col>
        <el-col :span="6">罐区域：<span v-text="showArea(info.areaId)"></span></el-col>
        <el-col :span="6">建筑形式：<span v-text="showBuild(info.buildingStyle)"></span></el-col>
      </el-row>
      <el-row class="info">
        <el-col :span="6">油料品种：<span v-text="showOil(info.oilType)"></span></el-col>
        <el-col :span="6">油面高度：<span>{{ info.oilHeight }}</span> m</el-col>
        <el-col :span="6">重量：<span>{{ info.oilStock }}</span> kg</el-col>
        <el-col :span="6">密度：<span>{{ info.oilDensity }}</span></el-col>
      </el-row>
      <el-row class="info">
        <el-col :span="6">公称容量：<span>{{ info.nominalCapacity }}</span> L</el-col>
        <el-col :span="6">实际容量：<span>{{ info.actualCapacity }}</span> L</el-col>
        <el-col :span="6">最大装油高度：<span>{{ info.maxHeight }}</span> m</el-col>
        <el-col :span="6">安全容量：<span>{{ info.safeCapacity }}</span> L</el-col>
      </el-row>
      <el-row class="info">
        <el-col :span="6">安全高度：<span>{{ info.safeHeight }}</span> m</el-col>
        <el-col :span="6">静态所定值：<span>{{ info.staticLockValue }}</span></el-col>
        <el-col :span="12">备注：<span>{{ info.remark }}</span></el-col>
      </el-row>
    </el-dialog>
  </div>
</template>

<script>
import system from '@/api/systemManagement'
import Pagination from '@/components/Pagination'

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
        safeCapacity: null,
        buildingStyle:null,
        remark:null,
        nominalCapacity:null,
        actualCapacity:null,
        maxHeight:null,
        safeHeight:null,
        oilNum:null,
        staticLockValue:null
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
          { required: true, message: '请选择油料品种', trigger: 'change' }
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
      areaOption: [],
      buildOption:[],
      page:1,

      tableVisible:false,
      info:{}
    }
  },
  computed: {
  },
  created() {
    this.getList()
    this.getOil().then(data => {
      this.oilOption = data
    })
    this.getArea().then(data => {
      this.areaOption = data
    })
    this.getBuilding().then(data => {
      this.buildOption = data
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
    showArea(val){
      for(let i=0;i<this.areaOption.length;i++){
        if (parseInt(this.areaOption[i].baseValue) === val) {
          return this.areaOption[i].baseKey
        }
      }
    },
    showBuild(val){
      for(let i=0;i<this.buildOption.length;i++){
        if (parseInt(this.buildOption[i].baseValue) === parseInt(val)) {
          return this.buildOption[i].baseKey
        }
      }
    },
    getList(val) {
      if(val){
        this.page = val.page
      }
      this.listLoading = true
      system.tankList(this.listQuery).then((r) => {
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
        tankName: null,
        oilType: null,
        oilHeight: null,
        oilStock: null,
        oilDensity: null,
        areaId: null,
        safeCapacity: null,
        buildingStyle:null,
        remark:null,
        nominalCapacity:null,
        actualCapacity:null,
        maxHeight:null,
        safeHeight:null,
        oilNum:null,
        staticLockValue:null
      }
    },
    // 创建
    handleCreate() {
      console.log(this.length)
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
          system.addTank(this.temp).then((r) => {
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
              // this.getList()
          }).catch(e => {
            this.listLoading = false
          })
        }
      })
    },
    // 更新
    handleUpdate(row) {
      this.temp = Object.assign({}, row) // copy obj
      console.log(typeof(this.temp.buildingStyle))
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
          system.modifyTank(this.temp).then((r) => {
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
          system.deleteTank({ id: row.id }).then((r) => {
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
    showDetail(row){
      this.tableVisible = true
      this.info = row
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

.el-col {
  margin-bottom:20px;
}
.l5 {
  padding-left:5px;
}
</style>
