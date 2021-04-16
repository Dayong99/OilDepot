<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.groupname" 
      placeholder="组名" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <el-button class="filter-item" type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
      <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="handleGroup">
        分组
      </el-button>
      <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="groupsCreate">
        组名管理
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

      <el-table-column label="组名" align="center" width="300">
        <template slot-scope="scope">
          <span>{{ scope.row.groupname }}</span>
        </template>
      </el-table-column>
      <el-table-column label="监控项" align="center">
        <template slot-scope="scope">
          <span v-text="showItem1(scope.row.itemId)"></span>
        </template>
      </el-table-column>
      <el-table-column label="说明" align="center" width="300">
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

    <el-dialog title="分组" :visible.sync="dialogFormVisible" @close="cancleDialog" width="740px">
      <el-form ref="dataForm" :rules="rules" :model="temp" label-position="right" label-width="85px" >
        <el-form-item label="组名" prop="id">
          <el-select
            v-model="temp.id"
            placeholder="请选择"
            clearable
            @change="clearItem"
          >
            <el-option
              v-for="item in nameOption"
              :key="item.id"
              :label="item.groupname"
              :value="item.id"
            />
          </el-select>
        </el-form-item>
        <!-- <el-form-item label="监控项" prop="itemId">
          <el-select
            v-model="group.itemId"
            placeholder="请选择监控项类型"
            clearable
            @change="getTypes"
            @clear="clearAll"
          >
            <el-option
              v-for="item in searchItem"
              :key="item.baseInfoId"
              :label="showItem(item.baseInfoId)"
              :value="item.baseInfoId"
            />
          </el-select>
          <el-select
            v-model="group.typeId"
            placeholder="请选择设备类型"
            clearable
              @change="getEquips"
          >
            <el-option
              v-for="item in searchEquipType"
              :key="item.equipType"
              :label="showEquip(item.equipType)"
              :value="item.equipType"
            />
          </el-select>
          <el-select
            v-model="temp.itemId"
            placeholder="请选择设备"
            clearable
             multiple
            filterable
            default-first-option
          >
            <el-option
              v-for="item in searchEquip"
              :key="item.id"
              :label="item.equipName"
              :value="item.id"
            />
          </el-select> -->
          <el-form-item label="监控项">
            <el-tree 
              ref="tree"
              lazy 
              node-key="value"
              :load="getOrgList" 
              :props="defaultProps" 
              highlight-current 
              show-checkbox>
            </el-tree>
          </el-form-item>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="createData">
          确认
        </el-button>
        <el-button @click="cancleDialog">
          取消
        </el-button>
      </div>
    </el-dialog>

    <el-dialog 
    :visible.sync="innerGroups"
    width="600px"
    :title="textMap[dialogStatus]" 
    @close="cancleGroups">
    <el-form
          ref="groupsForm"
          :rules="groupsRules"
          :model="groups"
          label-position="right"
          label-width="90px"
        >
          <el-form-item label="组名" prop="groupname">
            <el-input v-model="groups.groupname" clearable />
          </el-form-item>
          <el-form-item label="说明">
          <el-input
            type="textarea"
            v-model="groups.remark">
          </el-input>
        </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogStatus==='create'?createGroups():updateData()">
            确认
          </el-button>
          <el-button @click="cancleGroups">
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
      defaultProps: {
        children: "children",
        label: "label",
        isLeaf: 'leaf'
      },
      list: null,
      total: 0,
      dialogStatus: '',
      flag:1,
      textMap: {
        update: '编辑',
        create: '创建'
      },

      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        groupname:null
      },
      temp: {
        id: null,
        groupname: null,
        itemId:null,
        remark: null
      },

      // 树结构
      group:{
        itemId:null,
        typeId:null
      },
      dialogFormVisible: false,

      rules: {
        itemId: [
          { required: true, message: '请选择监控设备', trigger: 'change' }
        ],
        id: [
          { required: true, message: '请选择组名', trigger: 'change' }
        ]
      },
      
      // 添加监控项
      // 树
      searchItem: [],
      searchEquipType:[],
      searchEquip:[],
      nameOption:[],

      // 显示数据
      itemOption:[],
      equipOption:[],
      itemEquipOption:[],
      allItem:[],

      // 组名管理
      groups: {
        id: null,
        groupname: null,
        remark:null
      },
      groupsRules: {
        groupname: [
          { required: true, message: '组名不能为空', trigger: 'blur' }
        ]
      },
      groupsStatus: '',
      innerGroups: false
    }
  },
  computed: {
  },
  created() {
    this.getList()
    // 所有监控类型
    this.getMonitorType().then(data => {
      this.itemOption = data
    })
    // 所有监控设备
    this.getEquip().then(data => {
      this.itemEquipOption = data
    })
    // 所有监控设备类型
    this.getMonitorEquipType().then(data => {
      this.equipOption = data
    })
    // 所有监控设备类型
    this.getMonitorItem().then(data => {
      this.allItem = data
    })
    this.getItems()
    this.getName()
  },
  methods: {
    /**
             * 懒加载树获取组织机构子节点
             * element-tree使用方法
             * @param node:当前点击节点信息
             * @param resolve:传递参数方法
             * */
            getOrgList(node,resolve) {
                let self = this;
                // console.log(node);
                if(node.level == 0){
                     info.getItem().then((r) => {
                        let treeData = []
                        r.data.forEach(e => {
                            treeData.push({
                              label:this.showItem(e.baseInfoId),
                              // label:e.baseInfoId,
                              value:e.baseInfoId
                            })
                        })
                        resolve(treeData);
                    }).catch(res => {
                        resolve([]);
                    })
                }else if(node.level == 1){
                    // console.log("当前节点id值为："+node.data.value)
                    info.getType({baseInfoId:node.data.value}).then((r) => {
                        let treeData1 = [];
                        r.data.forEach(e => {
                            treeData1.push({
                              label:this.showEquip(e.equipType),
                              // label:e.equipType,
                              value:e.equipType
                            })
                        })
                        resolve(treeData1);
                    }).catch(res => {
                        resolve([]);
                    })

                }else{
                    // console.log("当前节点id值为："+node)
                    info.getEquip({baseInfoId:node.data.value,equipType:node.data.value}).then((r) => {
                        let treeData2 = [];
                        r.data.forEach(e => {
                            treeData2.push({
                              label:e.equipName,
                              value:e.equipId,
                              leaf:true
                            })
                        })
                        resolve(treeData2);
                    }).catch(res => {
                        resolve([]);
                    })

                }

            },         // 点击树  
    getName(){
      info.getName().then((r) => {
        if (r.code === 20000) {
          this.nameOption = r.data
        }
      }).catch(() => {
        this.$message.error('获取分组名称信息失败')
      })
    },
    // 添加监控项
    getItems(){
      info.getItem().then((r) => {
        if (r.code === 20000) {
          this.searchItem = r.data
        }
      }).catch(() => {
        this.$message.error('获取监控项信息失败')
      })
    },   
    getTypes (){
      this.group.typeId = null
      info.getType({baseInfoId:this.group.itemId}).then((r) => {
        if (r.code === 20000) {
          this.searchEquipType = r.data
        }
      }).catch(() => {
        this.$message.error('获取设备类型信息失败')
      })
    },  
    getEquips(){
      this.temp.itemId = null
      info.getEquip({baseInfoId:this.group.itemId,equipType:this.group.typeId}).then((r) => {
        if (r.code === 20000) {
          this.searchEquip = r.data
        }
      }).catch(() => {
        this.$message.error('获取设备信息失败')
      })
    },
    clearItem(){
      this.group = {
        itemId:null,
        typeId:null
      }
      this.temp.itemId = null
    },
    clearAll(){
      this.groups.typeId = null
      this.temp.itemId = null
      this.searchEquipType = []
      this.searchEquip = []
    },
    // 显示文字数据
    // 根据获得的baseInfoId显示对应的监控类型
    showItem(val){
      // console.log(this.itemOption)
      for(let i = 0;i<this.itemOption.length;i++){
        if(Number(val)===Number(this.itemOption[i].baseValue)){
          return this.itemOption[i].baseKey
        }
      }
    },

    // 根据获得的设备Id显示监控设备
    showEquip(val){
      // console.log(this.equipOption)
      for(let i = 0;i<this.equipOption.length;i++){
        if(Number(val)===Number(this.equipOption[i].baseValue)){
          return this.equipOption[i].baseKey
        }
      }
    },
    //表格中监控设备
    showItem1(val){
      // console.log(this.itemOption)
          let str = ''
          let arr
          if(val){
            if(val.indexOf(',')!==-1){
              arr = val.split(',')
            }else{
              arr = [val]
            }
            arr.sort()
            arr.forEach((item,index)=>{
              this.itemEquipOption.forEach((item1,index1)=>{
                if(Number(item)===Number(item1.id)){
                  if(index>0){
                      str += '，'+item1.equipName
                  }else {
                    str += item1.equipName
                  }
                }
              })
            })
        return str
      }
    },
    // 显示添加的监控设备
    showItems(val){
          let str = ''
          let arr
          let arr1 = []
          if(val){
            if(val.indexOf(',')!==-1){
              arr = val.split(',')
            }else{
              arr = [val]
            }
            arr.forEach((item,index)=>{
              this.allItem.forEach((item1,index1)=>{
                  if(Number(item)===Number(item1.id)){
                    arr1.push(item1.equipId)
                  }
              })
          })
          arr1.sort()
          arr1.forEach((item,index)=>{
            this.itemEquipOption.forEach((item1,index1)=>{
              if(Number(item)===Number(item1.id)){
                if(index>0){
                    str += '，'+item1.equipName
                }else {
                   str += item1.equipName
                }
              }
            })
          })
          return str
          }
          
      },
    getList(val) {
      this.listLoading = true
      info.monitorGroupList(this.listQuery).then((r) => {
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

    resetTemp() {
      this.temp = {
        id: null,
        groupname: null,
        itemId:null,
        remark: null
      }
      this.group = {
        itemId:null,
        typeId:null
      }
    },
    // 创建
    handleGroup() {
      this.resetTemp()
      this.dialogFormVisible = true
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    createData() {
      this.temp.itemId = []
      this.$refs.tree.getCheckedNodes(true).forEach((item,index)=>{
        this.temp.itemId.push(item.value)
      })
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          if(this.temp.itemId.length===0){
            this.$message.warning('请选择具体的监控项')
          }else{
            info.addGroup(this.temp).then((r) => {
              this.temp.itemId = this.temp.itemId.toString()
              this.dialogFormVisible = false
              this.getName()
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
            })
          }
          
        }
      })
    },
    // 重置弹窗
    cancleDialog() {
      this.dialogFormVisible = false
      console.log(this.$refs.tree.root)
      this.$refs.tree.setCheckedNodes([])
      // for(var i=0;i<this.$refs.tree.root.length;i++){
      //   this.$refs.tree.root[i].expanded = false;
      //   console.log()
      // }
      for(var i=0;i<this.$refs.tree.store._getAllNodes().length;i++){
　　　　　this.$refs.tree.store._getAllNodes()[i].expanded=false;
　　　}
      this.resetTemp()
    },

    // 组名管理
    resetGroups() {
      this.groups = {
        id: null,
        groupname: null,
        remark:null
      }
    },
    // 新建
    groupsCreate() {
      this.resetTemp()
      this.dialogStatus = 'create'
      this.innerGroups = true
      this.$nextTick(() => {
        this.$refs['groupsForm'].clearValidate()
      })
    },
    createGroups() {
      this.$refs['groupsForm'].validate((valid) => {
        if (valid) {
          info.addGroupName(this.groups).then((r) => {
            this.innerGroups = false
            this.getName()
            if (r.code === 20000) {
              this.$notify({
                title: '成功',
                message: '创建成功',
                type: 'success',
                duration: 2000
              })
              this.getList()
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
    handleUpdate(row) {
      this.groups.groupname = row.groupname
      this.groups.id = row.id
      this.groups.remark = row.remark
      this.innerGroups = true
      this.dialogStatus = 'update'
      this.$nextTick(() => {
        this.$refs['groupsForm'].clearValidate()
      })
    },
    updateData() {
      this.$refs['groupsForm'].validate((valid) => {
        if (valid) {
          info.modifyGroupName(this.groups).then((r) => {
            this.innerGroups = false
            this.getName()
            this.list.forEach((item,index)=>{
              if (Number(item.id) === Number(this.groups.id)) {
                item.groupname = this.groups.groupname
                item.remark = this.groups.remark
              }
            })
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
    handleModifyStatus(row, status) {
      this.handleDelete(row)
    },
    handleDelete(row) {
      this.$confirm('此操作将永久删除该条数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning',
          center: true
        }).then(() => {
          const index = this.list.indexOf(row)
          info.deleteGroupName({ id: row.id }).then((r) => {
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
    cancleGroups() {
      this.innerGroups = false
      this.resetGroups()
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
.el-tree {
  margin-top:7px;
}
</style>
