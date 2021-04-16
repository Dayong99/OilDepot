<template>
    <div>
        <div class="pieChart">
            <el-row :gutter="24" style="margin-top:20px;">
                <el-col :span="8">
                    <repertory-one></repertory-one>
                </el-col>
                <el-col :span="8">
                    <repertory-two></repertory-two>
                </el-col>
                <el-col :span="8">
                    <repertory-three></repertory-three>
                </el-col>
            </el-row>
        </div>
        <div class="repertoryBox">
            <el-row :gutter="24">
                <el-col :span="12">
                    <div class="circle"></div>
                    <span>总额</span>{{gasoline.sum.toFixed(3)}}
                    <span class="units">kg</span>
                </el-col>
                <!-- <el-col :span="8">使用率{{gasoline.useRate}}%</el-col> -->
                <el-col :span="12">
                    <span>剩余量</span>{{gasoline.allowance.toFixed(3)}}
                    <span class="units">kg</span>
                </el-col>
            </el-row>
            <el-row :gutter="24">
                <el-col :span="12">
                    <div class="circle" style="background:#f8b00a;"></div>
                    <span>总额</span>{{Diesel.sum.toFixed(3)}}
                    <span class="units">kg</span>
                </el-col>
                <!-- <el-col :span="8">使用率{{Diesel.useRate}}%</el-col> -->
                <el-col :span="12">
                    <span>剩余量</span>{{Diesel.allowance.toFixed(3)}}
                    <span class="units">kg</span>
                </el-col>
            </el-row>
            <el-row :gutter="24">
                <el-col :span="12">
                    <div class="circle" style="background:#01bd8d;"></div>
                    <span>总额</span>{{jetFuel.sum.toFixed(3)}}
                    <span class="units">kg</span>
                </el-col>
                <!-- <el-col :span="8">使用率{{jetFuel.useRate}}%</el-col> -->
                <el-col :span="12">
                    <span>剩余量</span>{{jetFuel.allowance.toFixed(3)}}
                    <span class="units">kg</span>
                </el-col>
            </el-row>
        </div>
    </div>
  
</template>

<script>
import RepertoryOne from './repertory/RepertoryOne'
import RepertoryTwo from './repertory/RepertoryTwo'
import RepertoryThree from './repertory/RepertoryThree'
import home from '@/api/home'

export default {
  components: { 
      RepertoryOne:RepertoryOne , 
      RepertoryTwo:RepertoryTwo,
      RepertoryThree:RepertoryThree
    },
    data(){
      return {
          gasoline:{
              sum:0,
              allowance:0
          },
          Diesel:{
              sum:0,
              allowance:0
          },
          jetFuel:{
              sum:0,
              allowance:0
          }
      }
    },
    created(){
        this.getInfo()
    },
    methods:{
        getInfo(){
            home.repertory().then((r) => {
                if (r.code === 20000) {
                    if(r.data.gasoline){
                        this.gasoline = r.data.gasoline
                    }
                    if(r.data.Diesel){
                        this.Diesel = r.data.Diesel
                    }
                    if(r.data.jetFuel){
                        this.jetFuel = r.data.jetFuel
                    }
                }
            }).catch(() => {
                this.$message.error('获取当前库存信息失败')
            })
        }
    }
}
</script>
<style scoped>
    .pieChart{
        margin-top:20x;
    }
    .el-row {
        color:white;
        font-size:14px;
    }
    .repertoryBox {
        width:100%;
        position:absolute;
        bottom: 0;
        padding:10px 20px 10px;
        text-align:left;
    }
    .repertoryBox .el-row {
        padding-bottom:10px;
    }
    .circle {
        width: 12px;
        height: 12px;
        background-color: #da0051;
        -moz-border-radius: 12px;
        -webkit-border-radius: 12px;
        border-radius: 12px;
        display:inline-block;
        position:absolute;
        left:15px;
        top:2px;
    }
    .el-col span {
        padding-left:25px;
        padding-right:5px;
        text-align: center;
        display: inline-block;
    }
    .el-col .units {
        padding-left:1px;
    }
</style>