<template>
  <div class="app-container">
    <div id="print" class="fileBox">
        <h1 class="title">油料清点证件</h1>
        <el-row :gutter="24" style="margin:20px;">
          <el-col :span="8">
            <span>单位:XX场站油料股</span>
          </el-col>
          <el-col :span="8" style="text-align:center;">
            <span>单位:公斤</span>
          </el-col>
          <el-col :span="8" style="text-align:right;">
            <span>清点日期:{{time}}</span>
          </el-col>
        </el-row>
          

        <table>
          <thead>
            <tr>
              <th rowspan="2">序<br>号</th>
              <th rowspan="2">油料<br>编码</th>
              <th rowspan="2">油料名称</th>
              <th rowspan="2">质量<br>情况</th>
              <th rowspan="2">规格</th>
              <th rowspan="2">战<br>备<br>数</th>
              <th rowspan="2">库存数</th>
              <th colspan="2">与账面比较</th>
              <th rowspan="2">备注</th>
            </tr>
            <tr style="width:200px;">
              <th>多出</th>
              <th>短少</th>
            </tr>
          </thead>
          <col>
          <col>
          <col style="width: 150px;">
          <col>
          <col>
          <col>
          <col>
          <col>
          <col>
          <tbody>
            <tr v-for="(item ,index) in list" :key="index">
              <td>{{item.index}}</td>
              <td>{{item.oilType}}</td>
              <td v-text="showOil(item.oilType)"></td>
              <td>{{item.quality}}</td>
              <td>{{item.standard}}</td>
              <td>{{item.combat}}</td>
              <td>{{item.stock}}</td>
               <td>{{item.compare}}</td>
              <td>{{item.compare}}</td>
              <td>{{item.remark}}</td>
            </tr>
          </tbody>
        </table>
        <dl class="remark">
          <span>股长：</span>
        </dl>

         <el-pagination
          @current-change="getPage"
        :current-page="page"
        layout="total, prev, pager, next"
        v-if="show"
        :page-count="count">
      </el-pagination> 
      </div>     
  </div>
</template>

<script>
import Pagination from '@/components/Pagination'
import { mapGetters } from 'vuex'
import table from '@/api/tableManagement'


export default {
  components: { Pagination },
  data() {
    return {
      // 加载列表
      // 表格
      oilOption:[],
      list: [],
      listLoading: true,
      listTotal: 0,
      listQuery: {
        pageSize: 10,
        pageNum: 1
      },
      obj:{
        index:null,
        combat: null,
        compare: null,
        id: null,
        oilType: null,
        quality: null,
        remark: null,
        standard: null,
        stock: null
      },
       count:0,
      page:1,
      show:true,

      time:null
    
    }
  },
  created(){
    this.getOil().then(data => {
      this.oilOption = data
    })
  },
  computed: {
    ...mapGetters([
      'id'
    ])
  },
  mounted() {
    this.getList()
    this.getTime()
  },
  methods: {
    getTime(){
      let date = new Date()
      this.time = date.getFullYear()+'年'+((date.getMonth()+1).toString().length<2?'0'+(date.getMonth()+1):(date.getMonth()+1))+'月'+(date.getDate().toString().length<2?'0'+date.getDate():date.getDate())+'日'
    },
    showOil(val){
      for(let i=0;i<this.oilOption.length;i++){
        if (parseInt(this.oilOption[i].baseValue) === parseInt(val)) {
          return this.oilOption[i].baseKey
        }
      }
    },
    // 加载列表
    getList() {
      this.list = []
      for (let i = 0; i < 10; i++) {
        this.list.push(this.obj)
      }
      this.listLoading = true
      table.oilInventory({
        pageSize: 10,
        pageNum: this.page
      }).then((r) => {
        if (r.code === 20000) {
          this.show = true
          r.data.rows.forEach((item, index) => {
            this.list[index] = item
            this.list[index].index = index+1
          })
          this.count = r.data.total
          this.listLoading = false
        }
      }).catch(() => {
        this.listLoading = false
      })
    },
    getPage(val){
      this.page = val
      this.getList()
    }
  }
}
</script>

<style scoped>
.filter-container {
  margin-bottom:30px;
}
.el-select {
    width:100px;
}
.el-input {
    width:150px;
}

.fileBox {
    width:1000px;
    margin:0 auto;
    font-family: '宋体';
    font-size: 18px;
}
.fileBox h1 {
  line-height:60px;
  letter-spacing: 3px;
  margin-bottom:20px;
  text-align: center;
  clear: both;
}

@page{ size:auto;margin:0mm; }

table {
  width:960px;
  margin:0 auto;
  border-top:2px solid black;
  border-left:2px solid black;
  border-collapse:collapse;
  table-layout:fixed;
}

table th {
  border-right:1px solid black;
  border-bottom:1px solid black;
  height:50px;
  margin:0;
  line-height:30px;
  letter-spacing: 5px;
}

table tr:first-child th:last-child {
  border-right:2px solid black;
}

table td {
  width:20%;
  height:40px;
  border-right:1px solid black;
  border-bottom:1px solid black;
  line-height:40px;
  text-align:center;
  /* width: 70px; */

}

table tr td:last-child {
    border-right:2px solid black;
}

table tr:last-child td {
  border-bottom:2px solid black;
}

.remark {
  width:90%;
  margin:10px auto;
  line-height: 38px;
  font-size: 20px;
}

.el-pagination {
  float:right;
}
</style>
