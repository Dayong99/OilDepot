<template>
  <div>
    <div class="controlBox">
      <el-checkbox-group v-model="checkedList" @change="changeRect">
        <el-checkbox v-for="item in rects" :label="item" :key="item" style="display:block;">{{item}}</el-checkbox>
      </el-checkbox-group>
      <el-checkbox v-model="line" @change="changeLine" style="display:block;">line</el-checkbox>
    </div>
    <div class="infoBox">
                <div class="infoTitle">
                  设备状态
                </div>
                <ul>
                  <li>当前设备：油罐一</li>
                  <li>工作状态：<span class="normal">正常</span></li>
                  <li>报警状态：<span class="alarm">报警</span></li>
                </ul>
              </div>
   <svg version="1.1" id="图层_1" xmlns="http://www.w3.org/2000/svg" xmlns:xlink="http://www.w3.org/1999/xlink" x="0px" y="0px"
	 viewBox="0 0 687 630" style="enable-background:new 0 0 687 630;" xml:space="preserve">
<rect x="485" y="152" class="st0" width="60" height="2"/>
<rect x="152.6" y="173" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.402 1.2171)" class="st1" width="1.4" height="10"/>
<rect x="174" y="173" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.4251 1.3872)" class="st1" width="1.6" height="16"/>
<rect x="174.6" y="201" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.6305 1.3919)" class="st1" width="1.4" height="12"/>
<rect x="163.8" y="201.7" transform="matrix(7.903548e-03 1 -1 7.903548e-03 376.5167 47.0433)" class="st1" width="1.5" height="23.1"/>
<rect x="78.1" y="181" transform="matrix(7.903548e-03 1 -1 7.903548e-03 334.4382 174.9537)" class="st1" width="2" height="150.1"/>
<rect x="127.5" y="-55.6" transform="matrix(7.903548e-03 1 -1 7.903548e-03 137.5188 -118.6355)" class="st2" width="2" height="131.1"/>
<polygon class="st2" points="376,11.9 376,10.7 456.5,9.5 456.5,10.8 " />
<polygon class="st2" points="376,333.3 376,331.7 456.5,330.5 456.5,332 "/>
<rect x="113" y="307.3" transform="matrix(7.903548e-03 1 -1 7.903548e-03 431.3396 201.7505)" class="st2" width="2" height="22"/>
<rect x="118.1" y="153.1" transform="matrix(7.903548e-03 1 -1 7.903548e-03 275.1178 36.8246)" class="st0" width="1.9" height="8"/>
<rect x="92.6" y="146.6" transform="matrix(7.903548e-03 1 -1 7.903548e-03 249.8472 62.351)" class="st0" width="1.8" height="21"/>
<rect x="103" y="317.9" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -2.8772 0.8334)" class="st2" width="2" height="93"/>
<rect x="104.2" y="436" transform="matrix(1 -7.903515e-03 7.903515e-03 1 -3.7825 0.846)" class="st2" width="1.9" height="86"/>
<rect x="65.1" y="9.3" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -2.0977 0.5305)" class="st2" width="1.9" height="512.7"/>
<rect x="84.4" y="157.3" transform="matrix(1 -7.903545e-03 7.903545e-03 1 -2.5831 0.6838)" class="st0" width="1.6" height="339.7"/>
<rect x="152.6" y="207" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.6826 1.2183)" class="st1" width="1.4" height="13"/>
<rect x="152.6" y="242" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.9592 1.2194)" class="st1" width="1.4" height="13"/>
<g>
	<rect x="123" y="0" class="st3" width="75" height="173"  id="rect1"/>
</g>
<g>
	<rect x="411" y="0" class="st3" width="75" height="173" id="rect2"/>
</g>
<g>
	<rect x="123" y="308" class="st3" width="75" height="173" id="rect3"/>
</g>
<polygon class="st4" points="-2100,-227 -2143.3,-152 -2056.7,-152 "/>
<g>
	<polygon class="st5" points="109.5,156.9 114.5,159.8 114.5,154 	"/>
	<polygon class="st5" points="109.5,156.9 104.5,154 104.5,159.8 	"/>
</g>
<g>
	<polygon class="st5" points="153.2,231.6 146,244 160.3,244 	"/>
	<polygon class="st5" points="153.2,231.6 160.3,219.2 146,219.2 	"/>
</g>
<g>
	<polygon class="st5" points="153.2,194.6 146,207 160.3,207 	"/>
	<polygon class="st5" points="153.2,194.6 160.3,182.2 146,182.2 	"/>
</g>
<g>
	<polygon class="st5" points="174.7,194.7 171,201 178.3,201 	"/>
	<polygon class="st5" points="174.7,194.7 178.3,188.4 171,188.4 	"/>
</g>
<rect x="442.6" y="173" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.393 3.5091)" class="st6" width="1.4" height="10"/>
<rect x="464" y="173" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.416 3.6792)" class="st6" width="1.6" height="16"/>
<rect x="464.6" y="201" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.6215 3.6839)" class="st6" width="1.4" height="12"/>
<rect x="453.8" y="201.7" transform="matrix(7.903548e-03 1 -1 7.903548e-03 664.2247 -242.9477)" class="st6" width="1.5" height="23.1"/>
<rect x="368" y="180" transform="matrix(7.903548e-03 1 -1 7.903548e-03 621.1384 -116.0213)" class="st6" width="2" height="150.1"/>
<rect x="442" y="207" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.6736 3.5063)" class="st6" width="1.6" height="13"/>
<rect x="442.6" y="242" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -1.9502 3.5114)" class="st6" width="1.4" height="13"/>
<g>
	<polygon class="st5" points="443.2,194.6 436,207 450.3,207 	"/>
	<polygon class="st5" points="443.2,194.6 450.3,182.2 436,182.2 	"/>
</g>
<g>
	<polygon class="st5" points="443.2,231.6 436,244 450.3,244 	"/>
	<polygon class="st5" points="443.2,231.6 450.3,219.2 436,219.2 	"/>
</g>
<g>
	<polygon class="st5" points="464.7,194.7 461,201 468.3,201 	"/>
	<polygon class="st5" points="464.7,194.7 468.3,188.4 461,188.4 	"/>
</g>
<rect x="438.6" y="480" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -3.8194 3.4911)" class="st1" width="2.4" height="10"/>
<rect x="459" y="480" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -3.8426 3.6533)" class="st1" width="2.6" height="16"/>
<rect x="460" y="508" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -4.0402 3.6579)" class="st1" width="1.6" height="10"/>
<rect x="449.8" y="506.2" transform="matrix(7.903548e-03 1 -1 7.903548e-03 964.7429 63.1496)" class="st1" width="1.5" height="23.1"/>
<rect x="438" y="514" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -4.1001 3.4859)" class="st1" width="2" height="13"/>
<rect x="438.9" y="549" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -4.6454 3.4954)" class="st1" width="2.1" height="81"/>
<g>
	<polygon class="st5" points="439.2,501.6 432,514 446.3,514 	"/>
	<polygon class="st5" points="439.2,501.6 446.3,489.2 432,489.2 	"/>
</g>
<g>
	<polygon class="st5" points="439.2,538.6 432,551 446.3,551 	"/>
	<polygon class="st5" points="439.2,538.6 446.3,526.2 432,526.2 	"/>
</g>
<g>
	<polygon class="st5" points="460.7,501.7 457,508 464.3,508 	"/>
	<polygon class="st5" points="460.7,501.7 464.3,495.4 457,495.4 	"/>
</g>
<g>
	<polygon class="st5" points="495.7,471.7 492,478 499.3,478 	"/>
	<polygon class="st5" points="495.7,471.7 499.3,465.4 492,465.4 	"/>
</g>
<g>
	<polygon class="st5" points="512.7,471.7 509,478 516.3,478 	"/>
	<polygon class="st5" points="512.7,471.7 516.3,465.4 509,465.4 	"/>
</g>
<g>
	<polygon class="st5" points="543.7,470.7 540,477 547.3,477 	"/>
	<polygon class="st5" points="543.7,470.7 547.3,464.4 540,464.4 	"/>
</g>
<g>
	<polygon class="st5" points="555.7,470.7 552,477 559.3,477 	"/>
	<polygon class="st5" points="555.7,470.7 559.3,464.4 552,464.4 	"/>
</g>
<rect x="146.6" y="481" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -3.8365 1.1793)" class="st7" width="1.4" height="10"/>
<rect x="168" y="481" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -3.8596 1.3494)" class="st6" width="1.6" height="16"/>
<rect x="168" y="509" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -4.0532 1.3501)" class="st6" width="1.6" height="9"/>
<rect x="157.8" y="506.2" transform="matrix(7.903548e-03 1 -1 7.903548e-03 675.0508 355.1405)" class="st6" width="1.5" height="23.1"/>
<rect x="145.3" y="414.7" transform="matrix(7.903554e-03 1 -1 7.903554e-03 707.5646 412.2893)" class="st6" width="1.5" height="296"/>
<rect x="146" y="515" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -4.1171 1.1765)" class="st6" width="1.6" height="13"/>
<rect x="146" y="550" transform="matrix(1 -7.903548e-03 7.903548e-03 1 -4.3938 1.1776)" class="st6" width="1.6" height="13"/>
<g>
	<polygon class="st5" points="147.2,502.6 140,515 154.3,515 	"/>
	<polygon class="st5" points="147.2,502.6 154.3,490.2 140,490.2 	"/>
</g>
<g>
	<polygon class="st5" points="67.2,427.6 60,440 74.3,440 	"/>
	<polygon class="st5" points="67.2,427.6 74.3,415.2 60,415.2 	"/>
</g>
<g>
	<polygon class="st5" points="104.2,422.6 97,435 111.3,435 	"/>
	<polygon class="st5" points="104.2,422.6 111.3,410.2 97,410.2 	"/>
</g>
<g>
	<polygon class="st5" points="86.2,403.6 79,416 93.3,416 	"/>
	<polygon class="st5" points="86.2,403.6 93.3,391.2 79,391.2 	"/>
</g>
<g>
	<polygon class="st5" points="147.2,539.6 140,552 154.3,552 	"/>
	<polygon class="st5" points="147.2,539.6 154.3,527.2 140,527.2 	"/>
</g>
<g>
	<polygon class="st5" points="168.7,502.7 165,509 172.3,509 	"/>
	<polygon class="st5" points="168.7,502.7 172.3,496.4 165,496.4 	"/>
</g>
<rect x="8" y="520" class="st2" width="679.5" height="2.1"/>
<rect x="25.5" y="495" class="st0" width="600" height="2.1"/>
<g>
	<rect x="411" y="308" class="st3" width="75" height="173" id="rect4"/>
</g>
<rect x="292" y="255" class="st6" width="2" height="307"/>
<rect x="543" y="153" class="st0" width="2" height="311"/>
<rect x="555" y="153" class="st0" width="2" height="311"/>
<rect x="39" y="627" class="st1" width="400" height="3"/>
<rect x="376" y="11" class="st2" width="2" height="485"/>
<rect x="486" y="439" class="st0" width="10" height="2"/>
<rect x="486" y="434" class="st0" width="28" height="2"/>
<rect x="495" y="439" class="st0" width="2" height="26"/>
<rect x="512" y="434" class="st0" width="2" height="31"/>
<rect x="495" y="479" class="st0" width="2" height="18"/>
<rect x="512" y="479" class="st0" width="2" height="18"/>
<rect x="542" y="478" class="st0" width="2" height="18"/>
<rect x="555" y="478" class="st0" width="2" height="18"/>
</svg>
<!-- <el-button type="primary" @click="changeLine">改变</el-button> -->
  </div>
</template>

<script>
export default {
    data(){
      return {
        checkedList: [],
        rects: ['rect1','rect2','rect3','rect4'],
        line: null
      }
    },
    beforeDestroy() {
    },
    created() {
    },
    methods:{
      changeRect(){
        console.log(this.checkedList)
        this.rects.forEach((item,index)=>{
          let rect = document.getElementById(item)
          if(this.checkedList.indexOf(item)!==-1){
            rect.classList.remove('st3')
            rect.classList.add('st2')
          }else{
            rect.classList.remove('st2')
            rect.classList.add('st3')
          }
        })
        
        // document.getElementById('rect1').classList.remove('st3')
        // document.getElementById('rect1').classList.add('st2')
        // document.getElementById('rect1').setAttribute('stroke','red')
      },
      changeLine(){
        let lineList
        if(this.line){
          lineList = document.querySelectorAll(".st6")
          lineList.forEach((item,index)=>{
            item.classList.remove('st6')
            item.classList.add('st8')
          })
        }else{
          lineList = document.querySelectorAll(".st8")
          console.log(lineList)
          lineList.forEach((item,index)=>{
            item.classList.remove('st8')
            item.classList.add('st6')
          })
        }
        
        console.log(this.line)
      }
    }
}
</script>

<style scoped>
	.st0{fill:#FF0000;}
	.st1{fill:#FF1D25;}
	.st2{fill:#00FF00;}
	.st3{fill:#3FA9F5;}
	.st4{fill:none;}
	.st5{fill:#29ABE2;}
	.st6{fill:#FBB03B;}
	.st7{fill:#F7931E;}
  .st8 {fill:white;}
  svg {
    width:90%;
    height:300px;
    position:absolute;
    top:30px;
    left:50%;
    margin-left:-45%;
  }
  .infoBox {
    z-index: 9999;
    width:200px;
    height:180px;
    position:absolute;
    right:0;
    bottom:0;
    /* background:rgba(18,50,86,0.7); */
    background:rgba(162,179,201,0.7);
    border:1px solid #1d4e7f;
    border-radius: 0 0 20px 0;
    color:white;
    font-size:16px;
  }
  .infoBox .infoTitle {
    background:#127dd3;
    padding-left:20px;
    line-height:35px;
  }
  .infoBox ul{
    margin:0;
    padding:0;
  }
  .infoBox ul li{
    list-style:none;
    font-size:14px;
    line-height:30px;
    padding-left:20px;
  }
  .normal {
    color:#3cfe33;
  }
  .alarm {
    color:#fe3341;
  }
  .el-checkbox {
    color:white;
  }
  .el-checkbox-group {
    position:absolute;
    right:0;
    top:20;
  }
</style>