<template>
  <div class="app-container">
    <div class="filter-container">
      <el-date-picker
        v-model="time"
        type="daterange"
        align="right"
        unlink-panels
        range-separator="至"
        start-placeholder="开始日期"
        end-placeholder="结束日期"
        value-format="yyyy-MM-dd"
        :picker-options="pickerOptions"
        style="width:400px;"
        @change="clearTime"
      ></el-date-picker>
      <!-- <el-select
        v-model="listQuery.fransferType"
        placeholder="作业任务"
        clearable
        style="width:200px;"
        @clear="handleFilter"
      >
        <el-option
          v-for="item in transferOption"
          :key="item.value"
          :label="item.label"
          :value="item.value"
        />
      </el-select>-->
      <el-button type="primary" icon="el-icon-search" @click="handleFilter">查询</el-button>
      <el-button type="primary" icon="el-icon-s-order" @click="dischargeOil">输油</el-button>
    </div>

    <el-table
      v-loading="listLoading"
      :data="list"
      element-loading-text="加载"
      border
      fit
      highlight-current-row
    >
      <el-table-column align="center" label="ID" width="50">
        <template slot-scope="scope">{{ scope.$index+1 }}</template>
      </el-table-column>
      <el-table-column label="日期" align="center" width="200">
        <template slot-scope="scope">{{ scope.row.createTime }}</template>
      </el-table-column>
      <el-table-column label="发给" align="center">
        <template slot-scope="scope">{{ scope.row.transferTo }}</template>
      </el-table-column>
      <el-table-column label="作业任务" align="center">
        <template slot-scope="scope">{{ scope.row.transferType | showType }}</template>
      </el-table-column>
      <el-table-column label="流水号" align="center">
        <template slot-scope="scope">{{ scope.row.serialNumber }}</template>
      </el-table-column>
      <el-table-column label="指挥员" align="center">
        <template slot-scope="scope">{{ scope.row.commander }}</template>
      </el-table-column>

      <el-table-column label="操作" align="center" width="150">
        <template slot-scope="{row}">
          <el-button type="primary" plain size="mini" @click="showDetail(row)">详情</el-button>
        </template>
      </el-table-column>
    </el-table>

    <pagination
      v-show="listTotal>0"
      :total="listTotal"
      :page.sync="listQuery.page"
      :limit.sync="listQuery.pageSize"
      @pagination="getList"
    />

    <el-dialog
      :visible.sync="dialogOilVisible"
      title="内外场输油"
      width="1260px"
      height="50%"
      @close="cancleForm"
    >
      <el-form
        :inline="true"
        ref="billForm"
        label-position="right"
        :rules="billRules"
        :model="billData"
        label-width="100px"
        size="small"
      >
        <el-row>
          <el-col :span="24">
            <el-form-item label="日期" prop="createTime">
              <el-date-picker
                v-model="billData.createTime"
                type="datetime"
                value-format="yyyy-MM-dd HH:mm:ss"
                :picker-options="pickerOptions1"
                style="width:220px;"
                placeholder="选择日期"
              ></el-date-picker>
            </el-form-item>
            <el-form-item label="发给" prop="transferTo">
              <el-input v-model="billData.transferTo" clearable />
            </el-form-item>
            <el-form-item label="流水号" prop="serialNumber">
              <el-input v-model="billData.serialNumber" clearable />
            </el-form-item>
            <el-form-item label="指挥员" prop="commander">
              <el-input v-model="billData.commander" clearable />
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="作业任务" prop="transferType" :inline-message="true">
              <span v-model="billData.transferType">输送3号喷气燃料</span>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <div class="processBox">
              <div class="title el-form-item__label">
                <span>工艺流程</span>
              </div>
              <div class="process">
                <el-select
                  v-model="billData.transferProcess1"
                  multiple
                  filterable
                  default-first-option
                  placeholder="请选择"
                  size="small"
                  @change="getProcessInfo"
                >
                  <el-option
                    v-for="item in processOptions1"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  />
                </el-select>
                <i class="el-icon-right" />
                <el-select
                  v-model="billData.transferProcess2"
                  multiple
                  filterable
                  default-first-option
                  placeholder="请选择"
                  size="small"
                  @change="getProcessInfo"
                >
                  <el-option
                    v-for="item in processOptions2"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  />
                </el-select>
                <i class="el-icon-right" />
                <el-select
                  v-model="billData.transferProcess3"
                  placeholder="请选择"
                  size="small"
                  @change="getProcessInfo"
                >
                  <el-option
                    v-for="item in processOptions3"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                  />
                </el-select>
              </div>
            </div>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="作业纪要" prop="jobSummary">
              <el-input
                type="textarea"
                placeholder="请输入相关内容"
                style="width:1010px;"
                v-model="billData.jobSummary"
              ></el-input>
            </el-form-item>
          </el-col>
        </el-row>
        <el-row>
          <el-col :span="24">
            <el-form-item label="油罐" prop="oilTankId">
              <el-select
                v-model="billData.oilTankId"
                placeholder="请选择"
                style="width:120px;"
                clearable
                @change="showData"
              >
                <el-option
                  v-for="item in tankOption"
                  :key="item.key"
                  :label="item.tankName"
                  :value="item.id"
                />
              </el-select>
            </el-form-item>
            <el-button type="success" plain icon="el-icon-plus" size="small" @click="addOilList">新增</el-button>
          </el-col>
        </el-row>
      </el-form>
      <el-divider v-if="dataShow" />
      <h3 v-if="dataShow">油罐测量数据</h3>
      <el-form
        v-for="(item, index) in formList"
        :key="index"
        :ref="'oilForm'+index"
        :inline="true"
        :rules="dataRules"
        :model="formList[index]"
        label-position="right"
        label-width="65px"
        size="small"
        class="oilForm"
        v-if="dataShow"
      >
        <el-form-item label="油罐" prop="oilTankId">
          <span v-text="showTank(item.oilTankId)" style="width:60px;display:inline-block;"></span>
        </el-form-item>
        <el-form-item label="作业前油高" prop="beforeHeight" label-width="110px">
          <el-input-number v-model="item.beforeHeight" :min="0" label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="作业后油高" prop="afterHeight" label-width="110px">
          <el-input-number v-model="item.afterHeight" :min="0" label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="密度" prop="oilDensity">
          <el-input-number v-model="item.oilDensity" :min="0" label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="作业量" prop="workload" label-width="80px">
          <el-input-number v-model="item.workload" :min="0" label="描述文字"></el-input-number>
        </el-form-item>
        <el-form-item label="进出" prop="jobType">
          <el-select placeholder="请选择" v-model="item.jobType" clearable style="width:90px;">
            <el-option
              v-for="item in directionOption"
              :key="item.value"
              :label="item.label"
              :value="item.value"
            />
          </el-select>
        </el-form-item>
        <el-form-item>
          <i class="el-icon-circle-close" @click.prevent="deleteOil(index)"></i>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button type="primary" @click="submitForm()">提交</el-button>
        <el-button @click="cancleForm">取消</el-button>
      </div>
    </el-dialog>

    <el-dialog title="输油详情" :visible.sync="tableVisible" width="1000px">
      <el-row class="info">
        <el-col :span="6">
          日期：
          <span>{{ info.createTime }}</span>
        </el-col>
        <el-col :span="6">
          发给：
          <span>{{ info.transferTo }}</span>
        </el-col>
        <el-col :span="6">
          流水号：
          <span>{{ info.serialNumber }}</span>
        </el-col>
        <el-col :span="6">
          指挥员：
          <span>{{ info.commander }}</span>
        </el-col>
      </el-row>
      <el-row class="info">
        <el-col :span="12">
          <span>作业任务：</span>
          {{info.transferType | showType}}
        </el-col>
        <el-col :span="12">
          <span>工艺流程：</span>
          {{info.transferProcess}}
        </el-col>
      </el-row>
      <el-row>
        <el-col :span="24">
          <span>作业纪要：</span>
          {{info.jobSummary}}
        </el-col>
      </el-row>
      <el-table :data="tableList" fit highlight-current-row style="margin-top:20px;">
        <el-table-column align="center" label="ID" width="50">
          <template slot-scope="scope">{{ scope.row.orderNum }}</template>
        </el-table-column>
        <el-table-column label="油罐" align="center">
          <template slot-scope="scope">
            <span v-text="showTank(scope.row.oilTankId)"></span>
          </template>
        </el-table-column>
        <el-table-column label="作业前油高" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.beforeHeight }}</span>
          </template>
        </el-table-column>
        <el-table-column label="作业后油高" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.afterHeight }}</span>
          </template>
        </el-table-column>
        <el-table-column label="密度" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.oilDensity }}</span>
          </template>
        </el-table-column>
        <el-table-column label="作业量" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.workload }}</span>
          </template>
        </el-table-column>
        <el-table-column label="进出" align="center">
          <template slot-scope="scope">
            <span>{{ scope.row.jobType===0?'进油':'出油' }}</span>
          </template>
        </el-table-column>
      </el-table>
    </el-dialog>
  </div>
</template>

<script>
import Pagination from "@/components/Pagination";
import { mapGetters } from "vuex";
import operation from "@/api/internalOperation";

export default {
  components: { Pagination },
  data() {
    return {
      transferOption: [
        {
          label: "倒罐",
          value: "0",
        },
        {
          label: "发油",
          value: "1",
        },
        {
          label: "输送3号喷气燃料",
          value: "2",
        },
      ],
      tankOption: [],
      directionOption: [
        {
          value: 0,
          label: "进油",
        },
        {
          value: 1,
          label: "出油",
        },
      ],
      processOptions1: [],
      processOptions2: [],
      processOptions3: [],
      // 查询
      pickerOptions: {
        shortcuts: [
          {
            text: "最近一周",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "最近一个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 30);
              picker.$emit("pick", [start, end]);
            },
          },
          {
            text: "最近三个月",
            onClick(picker) {
              const end = new Date();
              const start = new Date();
              start.setTime(start.getTime() - 3600 * 1000 * 24 * 90);
              picker.$emit("pick", [start, end]);
            },
          },
        ],
      },
      pickerOptions1: {
        shortcuts: [
          {
            text: "今天",
            onClick(picker) {
              picker.$emit("pick", new Date());
            },
          },
          {
            text: "昨天",
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24);
              picker.$emit("pick", date);
            },
          },
          {
            text: "一周前",
            onClick(picker) {
              const date = new Date();
              date.setTime(date.getTime() - 3600 * 1000 * 24 * 7);
              picker.$emit("pick", date);
            },
          },
        ],
      },
      // 加载列表
      // 表格
      list: [],
      listLoading: true,
      listTotal: 0,
      // 查询
      time: null,
      listQuery: {
        pageSize: 10,
        page: 1,
        beginTime: null,
        endTime: null,
        fransferType: 2,
      },

      // 新增弹窗
      dataShow: false,
      tankFlag: 0,
      dialogOilVisible: false,

      formList: [
        {
          oilTankId: null,
          beforeHeight: null,
          afterHeight: null,
          oilDensity: null,
          workload: null,
          orderNum: 1,
          jobType: 1,
        },
      ],
      params: [],
      obj: {},
      formObj: {},
      // 表单验证
      dischargeFlag: false,
      dischargeFlagArr: [],
      infoFlag: false,
      processFlag: false,
      formArr: [],
      transferProcess: "",
      processObj: {},
      billData: {
        createTime: null,
        transferProcess1: "",
        transferProcess2: "",
        transferProcess3: "",
        jobSummary: null,
        serialNumber: null,
        transferType: 2,
        commander: null,
        oilTankId: null,
        transferTo: null,
      },
      billRules: {
        createTime: [
          { required: true, message: "日期不能为空", trigger: "blur" },
        ],
        transferTo: [
          { required: true, message: "发给单位不能为空", trigger: "blur" },
        ],
        commander: [
          { required: true, message: "指挥员不能为空", trigger: "blur" },
        ],
        serialNumber: [
          { required: true, message: "流水号不能为空", trigger: "blur" },
        ],
        oilTankId: [
          { required: true, message: "请选择油罐", trigger: "change" },
        ],
      },
      dataRules: {
        beforeHeight: [
          { required: true, message: "作业前油高不能为空", trigger: "change" },
          {
            validator: (rule, value, callback) => {
              if (value == 0) {
                callback(new Error("不能为0"));
              } else {
                callback();
              }
            },
            trigger: "blur",
          },
        ],
        afterHeight: [
          { required: true, message: "作业后油高不能为空", trigger: "change" },
          {
            validator: (rule, value, callback) => {
              if (value == 0) {
                callback(new Error("不能为0"));
              } else {
                callback();
              }
            },
            trigger: "blur",
          },
        ],
        oilDensity: [
          { required: true, message: "密度不能为空", trigger: "change" },
          {
            validator: (rule, value, callback) => {
              if (value == 0) {
                callback(new Error("不能为0"));
              } else {
                callback();
              }
            },
            trigger: "blur",
          },
        ],
        workload: [
          { required: true, message: "作业量不能为空", trigger: "change" },
          // {
          //   validator: (rule, value, callback) => {
          //     if (value == 0) {
          //       callback(new Error("不能为0"));
          //     } else {
          //       callback();
          //     }
          //   },
          //   trigger: "blur",
          // },
        ],
        jobType: [{ required: true, message: "请选择进出", trigger: "change" }],
      },

      // 详情
      tableVisible: false,
      tableList: [],
      info: {},
    };
  },
  filters: {
    showType(val) {
      switch (parseInt(val)) {
        case 0:
          return "倒罐";
        case 1:
          return "发油";
        case 2:
          return "输送3号喷气燃料";
      }
    },
  },
  created() {
    this.getTank().then((data) => {
      this.tankOption = data;
    });
  },
  computed: {
    ...mapGetters(["id"]),
  },
  mounted() {
    this.getList();
    this.getProcess1();
    this.getProcess2();
    this.getProcess3();
  },
  methods: {
    getProcessInfo() {
      let str1 = "",
        str2 = "",
        str3 = "";
      if (this.billData.transferProcess1.length !== 0) {
        str1 = "从" + this.billData.transferProcess1 + ",";
      } else {
        str1 = "";
      }
      if (this.billData.transferProcess2.length !== 0) {
        str2 = "通过" + this.billData.transferProcess2 + ",";
      } else {
        str2 = "";
      }
      if (this.billData.transferProcess3.length !== 0) {
        str3 = "到" + this.billData.transferProcess3;
      } else {
        str3 = "";
      }
      this.billData.jobSummary = str1 + str2 + str3;
    },
    // 工艺流程
    getProcess1() {
      for (let i = 1; i < 18; i++) {
        this.processObj = {};
        if (i !== 17) {
          this.processObj.label = i + "号罐";
          this.processObj.value = i + "号罐";
          this.processOptions1.push(this.processObj);
        } else {
          this.processObj.label = "17栈桥";
          this.processObj.value = "17栈桥";
          this.processOptions1.push(this.processObj);
        }
      }
    },
    getProcess2() {
      for (let i = 1; i < 6; i++) {
        this.processObj = {};
        if (i !== 5) {
          this.processObj.label = i + "号泵";
          this.processObj.value = i + "号泵";
          this.processOptions2.push(this.processObj);
        } else {
          this.processObj.label = "5号自流";
          this.processObj.value = "5号自流";
          this.processOptions2.push(this.processObj);
        }
      }
    },
    getProcess3() {
      for (let i = 1; i < 20; i++) {
        this.processObj = {};
        // if (i < 17) {
        //   this.processObj.label = i + '号罐'
        //   this.processObj.value = i + '号罐'
        //   this.processOptions3.push(this.processObj)
        // } else
        if (i === 17) {
          this.processObj.label = "17储备库";
          this.processObj.value = "17储备库";
          this.processOptions3.push(this.processObj);
        } else if (i === 18) {
          this.processObj.label = "18加油线";
          this.processObj.value = "18加油线";
          this.processOptions3.push(this.processObj);
        } else if (i === 19) {
          this.processObj.label = "19零发油供应站";
          this.processObj.value = "19零发油供应站";
          this.processOptions3.push(this.processObj);
        }
      }
    },
    showProcess(val, process) {
      let processVal;
      if (val) {
        if (val.toString().search(",") !== -1) {
          // 只有一个值
          processVal = val;
        } else {
          processVal = val.split(",").join("→");
        }
      }
      return processVal;
    },
    showTank(val) {
      for (let i = 0; i < this.tankOption.length; i++) {
        if (val === this.tankOption[i].id) {
          return this.tankOption[i].tankName;
        }
      }
    },
    // 加载列表
    getList() {
      this.listLoading = true;
      operation
        .homeworkList(this.listQuery)
        .then((r) => {
          if (r.code === 20000) {
            this.list = r.data.Scores;
            this.listTotal = r.data.Count;
            this.listLoading = false;
          }
        })
        .catch(() => {
          this.listLoading = false;
        });
    },
    // 查询列表
    handleFilter() {
      if (this.time) {
        this.listQuery.beginTime = this.time[0];
        this.listQuery.endTime = this.time[1];
      } else {
        this.listQuery.beginTime = null;
        this.listQuery.endTime = null;
      }
      this.listQuery.page = 1;
      this.getList();
    },
    clearTime() {
      if (!this.time) {
        this.listQuery.beginTime = null;
        this.listQuery.endTime = null;
        this.listQuery.page = 1;
        this.getList();
      }
    },

    // 卸油
    // 重置新增列表
    resetFormList() {
      this.dataShow = false;
      this.tankFlag = 0;
      this.billData = {
        createTime: null,
        transferProcess1: "",
        transferProcess2: "",
        transferProcess3: "",
        jobSummary: null,
        serialNumber: null,
        transferType: 2,
        commander: null,
        oilTankId: null,
        transferTo: null,
      };
      this.formList = [
        {
          oilTankId: null,
          beforeHeight: null,
          afterHeight: null,
          oilDensity: null,
          workload: null,
          orderNum: 1,
          jobType: 1,
        },
      ];
      this.$refs["billForm"].resetFields();
      const len = document.querySelectorAll(".oilForm").length;
      for (let i = 0; i < len; i++) {
        this.formArr.push("oilForm" + i);
      }
      this.formArr.forEach((item, index) => {
        this.$refs[item][0].resetFields();
      });
    },
    // 打开新增弹窗
    dischargeOil() {
      this.dialogOilVisible = true;
    },
    // 显示测量数据
    showData() {
      if (this.billData.oilTankId && this.tankFlag === 0) {
        this.dataShow = true;
        this.formList[0].oilTankId = this.billData.oilTankId;
        this.tankFlag = 1;
      } else if (this.billData.oilTankId) {
        this.dataShow = true;
      }
    },
    // 新增油品条目
    addOilList() {
      if (this.dataShow) {
        this.formList.push({
          oilTankId: null,
          beforeHeight: null,
          afterHeight: null,
          oilDensity: null,
          workload: null,
          orderNum: null,
          jobType: 1,
        });
        let len = this.formList.length - 1;
        this.formList[len].oilTankId = this.billData.oilTankId;
        this.formList[len].orderNum = len + 1;
      }
    },
    // 删除油品条目
    deleteOil(index) {
      this.formList.splice(index, 1);
      if (this.formList.length === 0) {
        this.dataShow = false;
        this.tankFlag = 0;
        this.billData.oilTankId = null;
      }
    },
    // 提交表单
    submitForm() {
      console.log("11");
      // 验证支拨单表单
      this.$refs["billForm"].validate((valid) => {
        if (valid) {
          this.infoFlag = true;
        } else {
          // 验证不通过
          this.infoFlag = false;
          return false;
        }
      });
      if (!this.infoFlag) {
        return;
      }
      // 验证油罐测量数据信息
      this.formArr = [];
      const len = document.querySelectorAll(".oilForm").length;
      for (let i = 0; i < len; i++) {
        this.formArr.push("oilForm" + i);
      }
      this.formArr.forEach((item, index) => {
        this.$refs[item][0].validate((valid) => {
          if (valid) {
            this.dischargeFlag = true;
          } else {
            this.dischargeFlag = false;
            return false;
          }
        });
      });
      if(!this.dischargeFlag) {
        return
      }

      // 处理参数
      this.params = [];
      this.formList.forEach((item, index) => {
        this.formObj = {};
        this.formObj = item;
        this.params.push(this.formObj);
      });

      if (
        this.billData.transferProcess1.length === 0 &&
        this.billData.transferProcess2.length === 0 &&
        this.billData.transferProcess3.length === 0
      ) {
        this.processFlag = false;
      } else {
        if (this.billData.transferProcess1) {
          this.billData.transferProcess1 = this.billData.transferProcess1
            .toString()
            .split(",")
            .join("-");
        }
        if (this.billData.transferProcess2) {
          this.billData.transferProcess2 = this.billData.transferProcess2
            .toString()
            .split(",")
            .join("-");
        }
        if (this.billData.transferProcess3) {
          this.billData.transferProcess3 = this.billData.transferProcess3
            .toString()
            .split(",")
            .join("-");
        }
        this.processFlag = true;
      }

      // 提交信息
      if (this.infoFlag === false) {
        this.$message({
          message: "请填写完整内外场输油信息",
          type: "warning",
        });
      } else if (this.processFlag === false) {
        this.$message({
          message: "至少填写流程一",
          type: "warning",
        });
      } else if (this.dischargeFlag === false) {
        this.$message({
          message: "请填写完整油罐测量数据",
          type: "warning",
        });
      } else {
        const formParams = {
          createTime: this.billData.createTime,
          transferProcess1: this.billData.transferProcess1,
          transferProcess2: this.billData.transferProcess2,
          transferProcess3: this.billData.transferProcess3,
          jobSummary: this.billData.jobSummary,
          transferTo: this.billData.transferTo,
          serialNumber: this.billData.serialNumber,
          transferType: this.billData.transferType,
          commander: this.billData.commander,
          jobtransferrecords: this.params,
        };
        operation
          .sendOil(formParams)
          .then((r) => {
            console.log(r)
            if (r.code === 20000) {
              this.dialogOilVisible = false;
              this.$message({
                message: "输油成功",
                type: "success",
              });
              this.resetFormList();
              this.getList();
            }
          })
          .catch(() => {
            setTimeout(() => {
              this.$message({
                message: "输油失败",
                type: "error",
              });
            }, 500);
          });
      }
    },
    // 取消
    cancleForm() {
      this.dialogOilVisible = false;
      this.resetFormList();
    },

    // 查看详情
    showDetail(row) {
      this.tableVisible = true;
      this.getTableList(row);
      this.info = row;
      this.info.transferProcess = this.info.transferProcess
        .split(",")
        .join("→");
    },
    // 加载详情列表
    getTableList(row) {
      operation
        .homeworkDetail({
          jobtransferId: row.id,
        })
        .then((r) => {
          if (r.code === 20000) {
            this.tableList = r.data;
          }
        })
        .catch(() => {});
    },
  },
};
</script>

<style scoped>
.filter-container {
  margin-bottom: 30px;
}
.el-input {
  width: 150px;
}
.el-input-number {
  width: 115px;
}
.el-form-item i {
  color: #909399;
  font-size: 16px;
}
.info .el-col {
  margin-bottom: 20px;
}
h3 {
  margin-left: 10px;
}
.processBox {
  /* border-bottom:1px solid black;
  border-right:1px solid black;height:100px; */
  border-left: none;
  border-top: none;
  display: block;
  height: 40px;
}
.processBox .title {
  text-align: right;
  float: left;
}
.processBox .title span {
  width: 80px;
  line-height: 40px;
  font-weight: 700;
  float: left;
}
.processBox .process {
  float: left;
}

.processBox .process .el-select {
  width: 220px;
  margin-bottom: 20px;
}

.processBox .process .el-select:first-child {
  margin-left: 8px;
}
</style>
