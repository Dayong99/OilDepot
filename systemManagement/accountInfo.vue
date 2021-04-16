<template>
  <div class="app-container">
    <div class="filter-container">
      <el-input 
      v-model="listQuery.username" 
      placeholder="用户名" 
      class="filter-item" 
      clearable 
      @clear="handleFilter"
      @keyup.enter.native="handleFilter" />
      <!-- <el-select v-model="listQuery.importance" :placeholder="$t('table.importance')" clearable style="width: 90px" class="filter-item">
        <el-option v-for="item in importanceOptions" :key="item" :label="item" :value="item" />
      </el-select> -->
      <!-- <el-select v-model="listQuery.type" :placeholder="$t('table.type')" clearable class="filter-item" style="width: 130px">
        <el-option v-for="item in calendarTypeOptions" :key="item.key" :label="item.display_name+'('+item.key+')'" :value="item.key" />
      </el-select> -->
      <el-select 
      v-model="listQuery.sortOrder" 
      style="width: 140px" 
      class="filter-item"  
      clearable
      @cleaar="handleFilter"
      @change="handleFilter">
        <el-option 
        v-for="item in sortOptions" 
        :key="item.key" 
        :label="item.label" 
        :value="item.key" />
      </el-select>
      <el-button v-waves class="filter-item" type="primary" icon="el-icon-search" @click="handleFilter">
        查询
      </el-button>
      <!-- <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-edit" @click="handleCreate">
        添加
      </el-button> -->
      <el-button class="filter-item" style="margin-left: 10px;" type="primary" icon="el-icon-user-solid" @click="showRole">
        角色信息管理
      </el-button>
    </div>

    <el-table
      v-loading="listLoading"
      :key="tableKey"
      :data="list"
      border
      fit
      highlight-current-row
      style="width: 100%;"
      @sort-change="sortChange"
    >
      <el-table-column label="编号" prop="id" sortable="custom" align="center" width="80">
        <template slot-scope="scope">
          <span>{{ scope.row.userId }}
          </span>
        </template>
      </el-table-column>

      <el-table-column label="用户名" min-width="150px" max-width="200px" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.username }}</span>
        </template>
      </el-table-column>
      <el-table-column label="单位" min-width="150px" max-width="200px" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.position }}</span>
        </template>
      </el-table-column>
      <el-table-column label="角色" min-width="150px" max-width="200px" align="center">
        <template slot-scope="scope">
          <span v-text="getRoles(scope.row.roleId)"></span>
        </template>
      </el-table-column>

      <el-table-column label="说明" min-width="150px" max-width="200px" align="center">
        <template slot-scope="scope">
          <span>{{ scope.row.description }}</span>
        </template>
      </el-table-column>
      <el-table-column label="签名" width="200px" align="center">
        <template slot-scope="{row}">
          <el-button type="primary" plain size="mini" @click="showImg(row)">
            查看
          </el-button>
          <!-- 上传先不用 -->
          <!-- <el-button type="success" plain size="mini" @click="uploadFace(row)">
            上传
          </el-button> -->
        </template>
      </el-table-column>
      <el-table-column label="操作" align="center" width="150" class-name="small-padding fixed-width">
        <template slot-scope="{row}">
          <el-button type="primary" size="mini" @click="modifyPwd(row)">
            修改密码
          </el-button>
          <!-- <el-button type="success" size="mini" @click="handleUpdate(row)">
            编辑
          </el-button>
          <el-button size="mini" type="danger" @click="handleModifyStatus(row,'deleted')">
            删除
          </el-button> -->
        </template>
      </el-table-column>
    </el-table>

    <pagination v-show="total>0" :total="total" :page.sync="listQuery.pageNum" :limit.sync="listQuery.pageSize" @pagination="getUsers" />

    <el-dialog :title="textMap[dialogStatus]" :visible.sync="dialogFormVisible">
      <el-form 
      ref="dataForm" 
      :rules="rules" 
      :model="temp" 
      label-position="right" 
      label-width="90px" 
      >
        <el-form-item label="用户名" prop="username">
          <el-input v-model="temp.username" clearable/>
        </el-form-item>
        <el-form-item label="单位" prop="position">
          <el-input v-model="temp.position" clearable/>
        </el-form-item>
        <el-form-item label="角色" prop="role">
          <el-select v-model="temp.role" clearable>
            <el-option
              v-for="item in options"
              :key="item.baseValue"
              :label="item.baseKey"
              :value="item.baseValue"
            />
          </el-select>
        </el-form-item>
        <el-form-item label="说明">
          <el-input v-model="temp.description" :autosize="{ minRows: 2, maxRows: 4}" type="textarea" placeholder="说明" />
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="dialogStatus==='create'?createData():updateData()">
          确认
        </el-button>
        <el-button @click="dialogFormVisible = false">
          取消
        </el-button>
      </div>
    </el-dialog>

    <el-dialog :visible.sync="dialogImgVisible" title="签名" width="600px" style="text-align:center;">
      <el-image :src="imgUrl" fit>
        <div slot="error" class="image-slot">
          该用户未上传签名,请上传
          <i class="el-icon-picture-outline"/>
        </div>
      </el-image>
    </el-dialog>

    <el-dialog 
    :visible.sync="dialogUploadVisible" 
    title="头像上传" 
    width="600px" 
    style="line-height:40px;" 
    @close="closeUpload">
      <el-upload
        ref="upload"
        :limit="1"
        :data="userId"
        :auto-upload="true"
        :action="uploadFile()"
        :on-success="function (res,file) {return ModifySuccess(res,file)}"
        :on-error="function (res,file) {return ModifyFail(res,file)}"
        multiple
        list-type="picture"
        class="upload-demo"
      >
        <el-button type="primary" size="small">上传<i class="el-icon-upload el-icon-right" style="margin-left:5px;"/></el-button>
      </el-upload>
    </el-dialog>

    <el-dialog title="修改密码" :visible.sync="dialogPwdVisible" width="600px">
      <el-form ref="pwdForm" :rules="pwdRules" :model="pwdTemp" label-position="right" label-width="90px" style="width: 400px; margin-left:50px;">
        <el-form-item label="新密码" prop="pwd">
          <el-input v-model="pwdTemp.pwd" placeholder="请输入新密码" show-password clearable/>
        </el-form-item>
        <el-form-item label="确认密码" prop="pwd1">
          <el-input v-model="pwdTemp.pwd1" placeholder="请再次输入新密码" show-password clearable=""/>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="commitPwd()">
          确认
        </el-button>
        <el-button @click="dialogPwdVisible = false">
          取消
        </el-button>
      </div>
    </el-dialog>


    <el-dialog 
    :visible.sync="roleVisible"
    title="角色信息管理" >
    <el-dialog
        :visible.sync="innerRole"
        :title="textMap[roleStatus]"
        width="550px"
        append-to-body
      >
        <el-form
          ref="roleForm"
          :rules="roleRules"
          :model="role"
          label-position="right"
          label-width="90px"
        >
          <el-form-item label="角色名称" prop="key">
            <el-input v-model="role.key" clearable />
          </el-form-item>
        </el-form>
        <div slot="footer" class="dialog-footer">
          <el-button type="primary" @click="roleStatus==='create'?createRole():updateRole()">
            确认
          </el-button>
          <el-button @click="cancleRole">
            取消
          </el-button>
        </div>
      </el-dialog>
      <div class="filter-container">
        <el-button class="filter-item" type="primary" size="mini" icon="el-icon-edit" @click="roleCreate">
          添加
        </el-button>
      </div>

      <el-table
        :data="roleData"
        border
        fit
        highlight-current-row
        style="width: 100%;"
      >
        <el-table-column label="角色名称" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.baseKey }}</span>
          </template>
        </el-table-column>
        <el-table-column label="操作" align="center" width="100" class-name="small-padding fixed-width">
          <template slot-scope="{row}">
            <el-button size="mini" type="danger" @click="roleDelete(row,'deleted')">
              删除
            </el-button>
          </template>
        </el-table-column>
      </el-table>
    </el-dialog>
  </div>
</template>

<script>

import { parseTime } from '@/utils'
import waves from '@/directive/waves'
import Pagination from '@/components/Pagination' // Secondary package based on el-pagination
// import { mapGetters } from 'vuex'
import system from '@/api/systemManagement'

// const baseURL = process.env.BASE_API.replacereplace(/\"/g, '')
//  configURL.BASE_API.replace(/\"/g, '')

export default {
  name: 'User',
  components: { Pagination },
  directives: { waves },
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
    var validateUserName = (rule, value, callback) => {
      if (this.dialogStatus === 'update' && this.username === value) {
        callback()
      } else {
        system.repeatName({ name: value }).then((r) => {
          if (r && r.data) {
            callback(new Error('用户名重复'))
          } else {
            callback()
          }
        }).catch(e => {
          callback()
        })
      }
    }
    var validatePassword = (rule, value, callback) =>{
      if (this.pwdTemp.pwd === this.pwdTemp.pwd1) {
        callback()
      } else {
        callback(new Error('两次密码输入不一致'))
      }
    }
    return {
      tableKey: 0,
      list: null,
      total: 0,
      companys: [],
      listLoading: true,
      listQuery: {
        pageSize: 10,
        pageNum: 1,
        sortField: 'userId',
        sortOrder: 'desc',
        username: null,
        position: null
      },
      // importanceOptions: [1, 2, 3],
      // sortOptions: [{ label: '升序', key: 'asc' }, { label: '倒序', key: 'desc' }],
      statusOptions: ['published', 'draft', 'deleted'],
      showReviewer: false,
      temp: {
        userId: undefined,
        username: null,
        role: null,
        position: null,
        description: null
      },
      temp1: {},
      username: '',
      options: [],
      dialogFormVisible: false,
      dialogStatus: '',
      textMap: {
        update: '编辑',
        create: '创建'
      },

      rules: {
        username: [
          { required: true, message: '用户名称不能空', trigger: 'blur' },
          { validator: validateUserName, trigger: 'blur' }
        ],
        position: [
          { required: true, message: '请填写单位', trigger: 'blur' }
        ],
        role: [
          { required: true, message: '请选择角色', trigger: 'change' }
        ]
      },
      downloadLoading: false,

      // 查看质量证明书
      imgUrl: '',
      dialogImgVisible: false,

      // 上传头像
      dialogUploadVisible: false,
      user: {},
      userId: {},

      // 排序
      sortOptions: [{ label: '升序', key: 'asc' }, { label: '倒序', key: 'desc' }],

      //修改密码
      dialogPwdVisible:false,
      pwdTemp:{
        pwd:'',
        pwd1:'',
        username:''
      },
      pwdRules:{
        pwd: [
          { required: true, message: '新密码不能空', trigger: 'blur' }
        ],
        pwd1: [
          { required: true, message: '确认密码不能空', trigger: 'blur' },
          { validator: validatePassword, trigger: 'blur' }
        ]
      },

      // 角色信息管理
      roleVisible: false,
      roleQuery: {
        type:'人员类别'
      },
      role: {
        id: null,
        key: null,
        type:'人员类别'
      },
      roleRules: {
        key: [
          { required: true, message: '角色名称不能为空', trigger: 'blur' }
        ]
      },
      roleStatus: '',
      roleData: [],
      innerRole: false
    }
  },

  created() {
    this.getUsers()
    this.getRole()
  },
  methods: {
    getRoles(val){
      for(let i=0;i<this.options.length;i++){
        if (parseInt(this.options[i].baseValue) === parseInt(val)) {
          return this.options[i].baseKey
        }
      }
    },
    getUsers() {
      this.listLoading = true
      system.userList(this.listQuery).then((r) => {
        this.list = r.rows
        this.total = r.total
        this.listLoading = false
      }).catch((e) => {
        // console.log(e)
        this.listLoading = false
      })
    },
    handleFilter() {
      this.listQuery.pageNum = 1
      this.getUsers()
    },

    sortChange(data) {
      const { prop, order } = data
      if (prop === 'id') {
        this.sortByID(order)
      }
    },
    sortByID(order) {
      // if (order === 'ascending') {
      //   this.listQuery.sort = '+id'
      // } else {
      //   this.listQuery.sort = '-id'
      // }
      if (order === 'ascending') {
        this.listQuery.sortOrder = 'desc'
      } else {
        this.listQuery.sortOrder = 'asc'
      }
      this.handleFilter()
    },
    resetTemp() {
      this.temp = {
        userId: undefined,
        username: null,
        role: null,
        position: null,
        description: null
      }
    },
    // 上传路径
    uploadFile() {
      return process.env.VUE_APP_BASE_API + 'user/addFace'
    },
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
          system.addUser(this.temp).then((r) => {
            this.dialogFormVisible = false
            if (r.data === 0) {
              this.$notify({
                title: '失败',
                message: '名称重复',
                type: 'error',
                duration: 2000
              })
            } else {
              this.temp1 = this.temp
              this.temp1.userId = r.data
              this.temp1.roleId = this.temp.role
              this.list.unshift(this.temp1)
              this.$notify({
                title: '成功',
                message: '创建成功',
                type: 'success',
                duration: 2000
              })
            }
          }).catch(e => {
            this.listLoading = false
          })
        }
      })
    },
    // 编辑
    handleUpdate(row) {
      this.resetTemp()
      this.temp.userId = row.userId
      this.temp.username = row.username
      if(this.temp.role!==null){
        this.temp.role = Number(row.roleId)
      }
      this.temp.position = row.position
      this.temp.description = row.description
      // this.temp = Object.assign({}, row) // copy obj
      // this.temp.userId= row.userId
      this.dialogStatus = 'update'
      this.dialogFormVisible = true
      this.username = this.temp.username
      this.$nextTick(() => {
        this.$refs['dataForm'].clearValidate()
      })
    },
    updateData() {
      this.$refs['dataForm'].validate((valid) => {
        if (valid) {
          // const tempData = Object.assign({}, this.temp)
          // tempData.timestamp = +new Date(tempData.timestamp) // change Thu Nov 30 2017 16:41:05 GMT+0800 (CST) to 1512031311464
          system.modifyUser(this.temp).then((r) => {
            for (const v of this.list) {
              if (v.userId === this.temp.userId) {
                this.temp1 = this.temp
                this.temp1.roleId = this.temp.role
                console.log(this.temp1)
                const index = this.list.indexOf(v)
                this.list.splice(index, 1, this.temp1)
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
         system.deleteUser({ id: row.userId }).then((r) => {
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
    formatJson(filterVal, jsonData) {
      return jsonData.map(v => filterVal.map(j => {
        if (j === 'timestamp') {
          return parseTime(v[j])
        } else {
          return v[j]
        }
      }))
    },
    // 上传头像
    uploadFace(row) {
      this.dialogUploadVisible = true
      this.user = Object.assign({}, row)
      this.userId = { userId: row.userId }
    },
    // 关闭弹窗
    closeUpload() {
      this.dialogUploadVisible = false
      this.$refs.upload.clearFiles()
    },
    // 上传图片成功
    ModifySuccess(res, file) {
      this.$message({
        message: '用户头像上传成功',
        type: 'success'
      })
    },
    // 上传失败
    ModifyFail() {
      this.$message({
        message: '用户头像上传失败',
        type: 'error'
      })
    },
    // 查看头像
    showImg(row) {
      this.dialogImgVisible = true
      this.imgUrl = this.$baseURL + 'user/getface?id=' + row.userId + '&num=' + Math.random()
    },
    //修改密码
    modifyPwd(row){
      this.dialogPwdVisible = true
      this.pwdTemp.username = row.username
      this.$nextTick(() => {
        this.$refs['pwdForm'].clearValidate()
      })
    },
    commitPwd(){
      this.$refs['pwdForm'].validate((valid) => {
        if (valid) {
          system.modifyPwd({
            username:this.pwdTemp.username,
            password:this.pwdTemp.pwd
          }).then((r) => {
            this.dialogPwdVisible = false
            this.$notify({
              title: '成功',
              message: '修改成功',
              type: 'success',
              duration: 2000
            })
          }).catch(e => {
          })
        }
      })
    },

    // 角色信息管理
    showRole(row) {
      this.roleVisible = true
    },
    getRole() {
      system.roleList(this.roleQuery).then((r) => {
        if (r.code === 20000) {
          this.roleData = r.data
          this.options = r.data
        }
      }).catch(() => {
        this.$message.error('获取角色信息失败')
      })
    },
    // 新建
    resetRole() {
      this.role = {
        id: null,
        key: null,
        type:'人员类别'
      }
    },
    roleCreate() {
      this.innerRole = true
      this.roleStatus = 'create'
      this.$nextTick(() => {
        this.$refs['roleForm'].clearValidate()
      })
    },
    createRole() {
      this.$refs['roleForm'].validate((valid) => {
        if (valid) {
          system.addRole(this.role).then((r) => {
            this.innerRole = false
            if (r.code === 20000) {
              this.$notify({
                title: '成功',
                message: '创建成功',
                type: 'success',
                duration: 2000
              })
              this.getRole()
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
    // 删除
    roleDelete(row, status) {
      this.deleteRole(row)
    },
    deleteRole(row) {
      this.$confirm('此操作将永久删除该条数据, 是否继续?', '提示', {
          confirmButtonText: '确定',
          cancelButtonText: '取消',
          type: 'warning',
          center: true
        }).then(() => {
          system.deleteRole({ id: row.id }).then((r) => {
            this.$notify({
              title: '成功',
              message: '删除成功',
              type: 'success',
              duration: 2000
            })
            this.getRole()
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
    cancleRole() {
      this.innerRole = false
      this.resetRole()
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
.el-input , .el-select {
  width:200px;
}
</style>
