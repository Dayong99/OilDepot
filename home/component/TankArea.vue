<template>
  <div class="app-container">
    <svg
      version="1.1"
      id="图层_1"
      xmlns="http://www.w3.org/2000/svg"
      xmlns:xlink="http://www.w3.org/1999/xlink"
      x="0px"
      y="0px"
      viewBox="0 0 1972 1080"
      style="enable-background:new 0 0 1972 1080;"
      xml:space="preserve"
      @click="rectClick()"
    >
<!--      四角-->
      <!--      <circle cx="1972" cy="1080" r="9" class="st2 animated infinite flash slow"/>-->
      <!--      <circle cx="1972" cy="0" r="9" class="st2 animated infinite flash slow"/>-->
      <!--      <circle cx="0" cy="1080" r="9" class="st2 animated infinite flash slow"/>-->
      <!--      <circle cx="0" cy="0" r="9" class="st2 animated infinite flash slow"/>-->

      <!--      试画-->
      <!--      <path-->
      <!--        class="st02"-->
      <!--        d="M86.9,1069H48.8c-1.4,0 -2.6,-1.2 -2.6,-2.6V687.7c0,-2.5 2,-4.6 4.6,-4.6"-->
      <!--      />-->


      <rect x="1817.9" y="36.5" class="st0" width="126" height="37"/>
      <rect x="1817.9" y="79.1" class="st0" width="126" height="37"/>
      <rect x="1817.9" y="121.7" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="164.2" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="206.8" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="249.4" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="292" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="334.5" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="377.1" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="419.7" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="462.3" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="504.8" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="547.4" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="590" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="632.6" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="675.1" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="717.7" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="760.3" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="802.9" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="845.4" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="888" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="930.6" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="973.2" class="st0" width="126" height="37"/>
      <rect x="1818.1" y="1015.8" class="st0" width="126" height="37"/>


      <!--路线部分-->
      <!--      01path连接到小环路-->
      <path
        :class="params[13].view ? 'st011':'st1'"
        d="M1082.3,460.5v-207.5c0-2.9,2.4-5.3,5.3-5.3h34.5c2.7,0,4.8,2.2,4.8,4.8v450.7c0,2.8-2.3,5.1-5.1,5.1
        h-36.8c-3,0-5.4-2.4-5.4-5.4l2.6-243.2h-27.7c-3.3,0-6,2.7-6,6v177.3c0,3.5-2.8,6.4-6.4,6.4H32.8
        c-1.1,0-1.9,0.9-1.9,1.9v330.4c0,4.6,3.7,8.3,8.3,8.3h16q0.5,0.5 1,1v65q0.5,0.5 1,1h12"/>
      <!--      04path短线路-->
      <path
        :class="params[7].view ? 'st011':'st1'"
        d="M152.9 1056.9h30.3q0.5,-0.5 1,-1V932l0,42.9h31q0.1,-0.1 1,-1V932"/>
      <!--      05path-->
      <path
        :class="params[61].view ? 'st011':'st1'"
        d="M152.9 1056.9h30.3q0.5,-0.5 1,-1V932l0,42.9h31q0.1,-0.1 1,-1V932"/>
      <!--      06path-->
      <path class="st1" d="M369.2,965.8h-28.1c-2.9,0-5.2-2.3-5.2-5.2V650.2"/>
      <!--      09path-->
      <path
        :class="params[8].view ? 'st011':'st1'"
        d="M466.4,1057.5h26.7c1.5,0,2.8-1.2,2.8-2.8V932L495.5 962.5H525q0.5,-0.5 1,-1V932"/>
      <!--      10path-->
      <path
        :class="params[62].view ? 'st011':'st1'"
        d="M466.4,1057.5h26.7c1.5,0,2.8-1.2,2.8-2.8V932L495.5 962.5H525q0.5,-0.5 1,-1V932"/>
      <!--      15path-->
      <path
        :class="params[9].view ? 'st011':'st1'"
        d="M719.6,1058.2h13.6c2.1,0,3.8-2.7,3.8-4.8v-68.2V932L737.2,965.8H766q0.5,-0.5 1,-1V932"/>
      <!--      16path-->
      <path
        :class="params[63].view ? 'st011':'st1'"
        d="M719.6,1058.2h13.6c2.1,0,3.8-2.7,3.8-4.8v-68.2V932L737.2,965.8H766q0.5,-0.5 1,-1V932"/>
      <!--      19path 连接泵-->
      <path
        :class="params[10].view ? 'st011':'st1'"
        d="M906.2,1058H935c1.9,0,3.4-1.5,3.4-3.4V932L938 962H911.5q-0.5 -0.5 -1 -1V932"
      />
      <!--      20path-->
      <path
        :class="params[64].view ? 'st011':'st1'"
        d="M906.2,1058H935c1.9,0,3.4-1.5,3.4-3.4V932L938 962H911.5q-0.5 -0.5 -1 -1V932"
      />
      <!--      24path复杂长线路-->
      <path
        :class="[params[7].view, params[8].view, params[9].view, params[10].view, params[11].view].some(function (item, index, arr) {
          return item === true
        }) ? 'st011':'st1'"
        d="M174.9,222.5v594.8c0,2.9,2.3,5.2,5.2,5.2h926.8c1.7,0,3,1.3,3,3V932L1109,824.8q0,-1 -3,-2H910.2V932L910.2,823H737V932L737.4,823H495.9V932L495.9,823H184.2V932"
      />
      <!--      24path短线路-->
      <path
        :class="params[11].view ? 'st011':'st1'"
        d="M1092,1056.2h14.2c2.1,0,3.8-2.7,3.8-4.8V932L1110.3 959L1147 959q0.5 -0.5 1,-1V932"/>
      <!--      25path短线路-->
      <path
        :class="params[65].view ? 'st011':'st1'"
        d="M1092,1056.2h14.2c2.1,0,3.8-2.7,3.8-4.8V932L1110.3 959L1147 959q0.5 -0.5 1,-1V932"/>
      <!--      25path复杂长线路-->
      <path
        :class="[params[61].view, params[62].view, params[63].view, params[64].view, params[65].view].some(function (item, index, arr) {
      return item === true
      }) ? 'st011':'st1'"
        d="M885,223L834.4,223c-1.9,0-3.5,1.6-3.5,3.5v32.8c0,2.1-1.7,3.8-3.8,3.8
        H219.3c-1.7,0-3.1,1.4-3.1,3.1V932L216.5 787.8H526.1V932L526.1 787.8
        H767V932L767 787.8H938V932L938 787.8H1146.8q0.5 0.5 1 1V932"
      />
      <!--      27path-->
      <path
        :class="params[1].view ? 'st011':'st1'"
        d="M151.9,95.5v123.8c0,1.8,1.5,3.3,3.3,3.3H174.5c0 0,1.1 1.1,1.1 1.1"
      />
      <!--      28path-->
      <path :class="params[2].view ? 'st011':'st1'"
            d="M281.9,101.5V216c0,3.4 -2.8,6.2 -6.2,6.2H178q-3,0 -3,3"/>
      <!--      29path-->
      <path :class="params[3].view ? 'st011':'st1'"
            d="M410.9,101.5V216.5c0,3.4 -2.8,6.2 -6.2,6.2H178q-3,0 -3,3"/>
      <!--      30path-->
      <path :class="params[4].view ? 'st011':'st1'"
            d="M537.9,101.5V216.5c0,3.4 -2.8,6.2 -6.2,6.2H178q-3,0 -3,3"/>
      <!--      31path-->
      <path :class="params[5].view ? 'st011':'st1'"
            d="M656,101.5V216.5c0,3.4 -2.8,6.2 -6.2,6.2H178q-3,0 -3,3"/>
      <!--      32path-->
      <path :class="params[6].view ? 'st011':'st1'"
            d="M769.8,101.5V216.5c0,3.4 -2.8,6.2 -6.2,6.2H178q-3,0 -3,3"/>
      <!--      33path-->
      <path :class="params[55].view ? 'st011':'st1'"
            d="M888.2,111.8L888.2,222q-0.5,0.5 -1,1H885"/>
      <!--      34path-->
      <path :class="params[56].view ? 'st011':'st1'"
            d="M1015.6,111.8L1015.6,222q-0.5,0.5 -1,1H885"/>
      <!--      35path-->
      <path :class="params[57].view ? 'st011':'st1'"
            d="M1144.2,111.8L1144.2,222q-0.5,0.5 -1,1H885"/>
      <!--      36path-->
      <path :class="params[58].view ? 'st011':'st1'"
            d="M1270.2,111.8L1270.2,222q-0.5,0.5 -1,1H885"/>
      <!--      37path-->
      <path :class="params[59].view ? 'st011':'st1'"
            d="M1391.4,111.8L1391.4,222q-0.5,0.5 -1,1H885"/>
      <!--      38path-->
      <path :class="params[60].view ? 'st011':'st1'"
            d="M1514.2,111.8L1514.2,222q-0.5,0.5 -1,1H885"/>

      <!--      40path连接46沉淀罐-->
      <path
        :class="params[41].view ? 'st011':'st1'"
        d="M1205.8,957.5v-81.7c0-1.5-1.2-2.7-2.7-2.7H692.8c-2.5,0-4.6-2.1-4.6-4.6V583.3c0-4.1-3.3-7.4-7.4-7.4H427.1
        c-4.5,0-8.2-3.7-8.2-8.2V441.1c0-5.5,4.5-10,10-10h82.7V385c0-4.7,3.8-8.5,8.5-8.5h35.9"
      />
      <!--      47沉淀罐左侧下小弯-->
      <path
        :class="params[41].view ? 'st011':'st1'"
        d="M511.6,431.2v44.6c0,4.6,3.7,8.2,8.2,8.2h36.1"/>
      <!--      41path连接沉淀罐右侧把手形状管路-->
      <path
        :class="params[42].view ? 'st011':'st1'"
        d="M1262.9,1057.5h17.7c2,0,3.7-1.7,3.7-3.7V864.9c0-1.9-1.5-3.4-3.4-3.4H860.4c-3,0-5.5-2.4-5.5-5.5V443
        c0-6.6-5.3-11.9-11.9-11.9h-93.1"
      />
      <!--      46、47沉淀罐右侧把手形状管路-->
      <path
        :class="params[42].view ? 'st011':'st1'"
        d="M708.9,376.5h32.6c4.7,0,8.4,3.8,8.4,8.4v46.2v41.2c0,6.5-5.2,11.7-11.7,11.7h-34.3"
      />
      <!--      42、39相连接path-->
      <path
        :class="[params[39].view, params[40].view].every(function (item, index, arr) {
          return item === true
        }) ? 'st011':'st1'"
        d="M33.9,101.5v112.4c0,3.7,3,6.6,6.6,6.6h35.3c3.3,0,6,2.7,6,6v399.1c0,1.6,1.3,2.9,2.9,2.9H417
        c3.2,0,5.9,2.6,5.9,5.9v265c0,3.4,2.7,6.1,6.1,6.1h741.8c3.3,0,6,2.7,6,6v42.8c0,1.7,1.4,3.1,3.1,3.1h25.9v97.8c0,1.2,1,2.2,2.2,2.2
        h14.8"
      />
      <!--      104小环路-->
      <path
        :class="params[66].view ? 'st011':'st1'"
        d="M1048.2,225v49.6c0,1.8-1.5,3.3-3.3,3.3h-36.3c-1.7,0-3.2,1.4-3.2,3.2v55.4c0,1.9,1.5,3.4,3.4,3.4h74
        v-87c0-2.9,2.4-5.3,5.3-5.3h34.5c2.7,0,4.8,2.2,4.8,4.8v450.7c0,2.8-2.3,5.1-5.1,5.1
        h-36.8c-3,0-5.4-2.4-5.4-5.4L1082.9,341.5"
      />

      <!--控制器部分-->

      <!--      st21黄色 st2、st3红色 st4、st5绿色-->
<!--      01号控制器-->
      <circle cx="30.9" cy="929.4" r="9"
              :class="params[13].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 25.3487 933.2)"
        class="st8 st6 st10 animated infinite flash slow"
      >01</text>
      <!--      04号控制器-->
      <circle cx="184.6" cy="930.3" r="9"
              :class="params[7].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 178.0986 934.7063)"
        class="st8 st6 st10 animated infinite flash slow"
      >04</text>
      <!--      05号控制器-->
      <circle cx="216.2" cy="930.6" r="9"
              :class="params[61].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 209.6818 934.34)"
        class="st8 st6 st10 animated infinite flash slow"
      >05</text>
      <!--      06号控制器-->
      <circle cx="335.9" cy="929.6" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 329.3485 933.34)"
        class="st8 st6 st10 animated infinite flash slow"
      >06</text>
      <!--      09号控制器-->
      <circle cx="495.9" cy="929.6" r="9"
              :class="params[8].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 489.3486 933.3394)"
        class="st8 st6 st10 animated infinite flash slow"
      >09</text>
      <!--      10号控制器-->
      <circle cx="525.4" cy="929.6" r="9"
              :class="params[62].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 518.8487 933.3392)"
        class="st8 st6 st10 animated infinite flash slow"
      >10</text>
      <!--      15号控制器-->
      <circle cx="736.9" cy="930.3" r="9"
              :class="params[9].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 730.3487 934.0067)"
        class="st8 st6 st10 animated infinite flash slow"
      >15</text>
      <!--      16号控制器-->
      <circle cx="766.2" cy="929.3" r="9"
              :class="params[63].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 759.6819 933.0059)"
        class="st8 st6 st10 animated infinite flash slow"
      >16</text>
      <!--      19号控制器-->
      <circle cx="910.4" cy="929.3" r="9"
              :class="params[10].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 903.8274 933.0067)"
        class="st8 st6 st10 animated infinite flash slow"
      >19</text>
      <!--      20号控制器-->
      <circle cx="938.2" cy="929.3" r="9"
              :class="params[64].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 931.6819 933.0067)"
        class="st8 st6 st10 animated infinite flash slow"
      >20</text>
      <!--      24号短线路控制器-->
      <circle cx="1109.9" cy="929.6" r="9"
              :class="params[11].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1103.349 933.3401)"
        class="st8 st6 st10 animated infinite flash slow"
      >24</text>
      <!--      25号控制器-->
      <circle cx="1147.6" cy="929.3" r="9"
              :class="params[65].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      />
      <text
        transform="matrix(1 0 0 1 1141.0155 933.0062)"
        class="st8 st6 st10 animated infinite flash slow"
      >25</text>
      <!--      27号控制器-->
      <circle cx="151.9" cy="162.3" r="9"
              :class="params[1].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 145.3486 166.0894)"
        class="st8 st6 st10 animated infinite flash slow"
      >27</text>
      <!--      28号控制器-->
      <circle cx="281.9" cy="162" r="9"
              :class="params[2].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 275.3487 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >28</text>
      <!--      29号控制器-->
      <circle cx="410.9" cy="162" r="9"
              :class="params[3].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 404.3488 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >29</text>
      <!--      30号控制器-->
      <circle cx="537.9" cy="162" r="9"
              :class="params[4].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 531.3488 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >30</text>
      <!--      31号控制器-->
      <circle cx="654.9" cy="162" r="9"
              :class="params[5].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 648.3488 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >31</text>
      <!--      32号控制器-->
      <circle cx="769.9" cy="162" r="9"
              :class="params[6].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 763.3489 165.6665)"
        class="st8 st6 st10 animated infinite flash slow"
      >32</text>
      <!--      33号控制器-->
      <circle cx="888.2" cy="162" r="9"
              :class="params[55].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 881.6821 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >33</text>
      <!--      34号控制器-->
      <circle cx="1015.6" cy="162" r="9"
              :class="params[56].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1009.0149 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >34</text>
      <!--      35号控制器-->
      <circle cx="1144.2" cy="162.5" r="9"
              :class="params[57].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1137.6819 166.2561)"
        class="st8 st6 st10 animated infinite flash slow"
      >35</text>
      <!--      36号控制器-->
      <circle cx="1269.9" cy="162" r="9"
              :class="params[58].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1263.3799 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >36</text>
      <!--      37号控制器-->
      <circle cx="1391.4" cy="162" r="9"
              :class="params[59].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1384.8485 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >37</text>
      <!--      38号控制器-->
      <circle cx="1514.2" cy="162" r="9"
              :class="params[60].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1507.6823 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >38</text>
      <!--      39号控制器-->
      <circle cx="1177.2" cy="929.3" r="9"
              :class="params[40].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1170.6815 933.0065)"
        class="st8 st6 st10 animated infinite flash slow"
      >39</text>
      <!--      40号控制器-->
      <circle cx="1205.9" cy="929.3" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 1199.3481 933.0067)"
        class="st8 st6 st10 animated infinite flash slow"
      >40</text>
      <!--      41号控制器-->
      <circle cx="1284.4" cy="929.3" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 1277.8167 933.0061)"
        class="st8 st6 st10 animated infinite flash slow"
      >41</text>
      <!--      42号控制器-->
      <circle cx="33.9" cy="162.3" r="9"
              :class="params[39].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <!--104号控制器---中间左边一个小环路上边油路控制器-->
      <circle cx="1005.6" cy="285.5" r="9"
              :class="params[66].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 996.6 289.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >103</text>

      <!--      st8白色-->
      <text
        transform="matrix(1 0 0 1 27.3487 166.0893)"
        class="st8 st6 st10 animated infinite flash slow"
      >42</text>

      <!--      02path大一环路-->
      <path
        :class="params[12].view ? 'st011':'st1'"
        d="M86.9,1057.8H58.8c-1.4,0-2.6-1.2-2.6-2.6V687.7c0-2.5,2-4.6,4.6-4.6h1000.8c3,0,5.4,2.4,5.4,5.4v114.3
        c0,3.1,2.5,5.6,5.6,5.6H1371c2.5,0,4.5,2,4.5,4.5v64.3c0,2.5,2,4.5,4.5,4.5h138.3c1.7,0,3-1.3,3-3V207c0-2.1,1.7-3.8,3.8-3.8h75.7
        v77.8c0,2.5,2,4.6,4.6,4.6h12.5c2.6,0,4.7-2.1,4.7-4.7V74.7c0-2.5-2-4.6-4.6-4.6h-12.2c-2.7,0-4.9,2.2-4.9,4.9v141.5"
      />
      <circle cx="56.2" cy="929.4" r="9"
              :class="params[12].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 49.6821 933.1732)"
        class="st8 st6 st10 animated infinite flash slow"
      >02</text>

      <!--03path连接到大二环路-->
      <!--      c-2.1,0 -3.8,-1.7 -3.8,-3.8-->
      <path
        :class="params[14].view ? 'st011':'st1'"
        d="M86.9,1057.8H57.4q-0.5,-0.5 -1,-1v-66q0.5,-0.5 1,-1h21.2q0.5,-0.5 1,-1V712.2c0-1.1,0.9-1.9,1.9-1.9h142.3h247h268.8h308.2c2.4,0,4.3,1.9,4.3,4.3v114
        c0,3.5,2.8,6.3,6.3,6.3h303.2c3.3,0,6,2.7,6,6v57.4c0,3.8,3.1,6.9,6.9,6.9h157.9c3.7,0,6.8-3,6.8-6.8V459.9c0-3.4,2.7-6.1,6.1-6.1
        h53.8v92.5c0,2.3,1.9,4.2,4.2,4.2h14.2c2,0,3.6-1.6,3.6-3.6V322.6c0-3.2-2.6-5.8-5.8-5.8h-8c-4.9,0-8.9,4-8.8,8.9L1598,456"
      />
      <circle cx="78.8" cy="929.4" r="9"
              :class="params[14].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 72.2654 933.1733)"
        class="st8 st6 st10 animated infinite flash slow"
      >03</text>

      <!--      07line-->
      <path class="st1" d="M409.6,1058.2h-34.9c-1.7,0-3.1-1.4-3.1-3.1V683.2"/>
      <circle cx="371.6" cy="929.6" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 365.0154 933.3394)"
        class="st8 st6 st10 animated infinite flash slow"
      >07</text>

      <!--      08line-->
      <path class="st1" d="M371.6,965.8h25.8c2.7,0,4.9-2.2,4.9-4.9V708.5"/>
      <circle cx="402.2" cy="929.6" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 395.6819 934.3916)"
        class="st8 st6 st10 animated infinite flash slow"
      >08</text>


      <!--      11line 连接到泵-->
      <path
        class="st1"
        d="M658.4,1057.2h-44.1c-2.2,0-3.9-1.8-3.9-3.9v-87.4h-50.9c-2,0-3.6-1.6-3.6-3.6V683.2"
      />
      <!--      13path大三环路-->
      <path
        class="st06"
        d="M616.4,963.5V737.2c0-3.4,2.8-6.2,6.2-6.2h723.5c3.2,0,5.8,2.6,5.8,5.8v183.4c0,2.7,2.2,4.8,4.8,4.8h202.1
        c2.5,0,4.6-2,4.6-4.6V709.1c0-1.7,1.4-3.1,3.1-3.1h30.4v88.8c0,3.1,2.5,5.7,5.7,5.7h13.2c3,0,5.5-2.5,5.5-5.5v-211
        c0-2.5-2-4.5-4.5-4.5h-15.2c-2.6,0-4.7,2.1-4.7,4.7v130.3"
      />
      <!--      14path大四环路-->
      <path
        class="st010"
        d="M610.4,965.8h35.1c2.2,0,3.9-1.8,3.9-3.9V764.1c0-2.5,2-4.6,4.6-4.6h668.5c2.5,0,4.4,2,4.4,4.4v177.8
        c0,2.4,1.9,4.3,4.3,4.3h247.7h19v72.7c0,2.7,2.2,4.8,4.8,4.8h11.9c3.1,0,5.6-2.5,5.6-5.6v-177c0-2.1-1.7-3.9-3.9-3.9h-14.1
        c-2.2,0-3.9,1.8-3.9,3.9l-0.5,105.1"
      />
      <!--      17line 连接泵-->
      <path
        class="st1"
        d="M849.4,1058h-15.9c-2.7,0-4.9-2.2-4.9-4.9v-94h-30.6c-2.1,0-3.8-1.7-3.8-3.8V731"
      />


      <!--      21、22path-->
      <path class="st1" d="M998.9,759.5l-3,203h-26.2c-1.9,0-3.5-1.6-3.5-3.5V731"/>
      <!--      23path长线路-->
      <!--      animated infinite flash slow-->
      <!--      :class="params[2].view ? 'st2 animated flash infinite slow' : 'st4 animated flash infinite slow'"-->
      <path
        class="st01"
        d="M136.9,189.5v652.8c0,2.9,2.3,5.2,5.2,5.2h884.8c1.7,0,3,1.3,3,3v108.4c0,2-1.6,3.6-3.6,3.6h-30.4v91.6
        c0,1.9,1.5,3.4,3.4,3.4h37.6"
      />

      <!--      26path连接大一环路-->
      <path class="st02" d="M51.1,101.5V186c0,1.9,1.6,3.5,3.5,3.5h1543.8"/>

      <!--      12line-->
      <line class="st1" x1="586.9" y1="963.5" x2="586.9" y2="710.3"/>

      <!--      18line-->
      <line class="st1" x1="828.7" y1="759.5" x2="828.7" y2="963.5"/>


      <!--      43line-->
      <line class="st01" x1="167.9" y1="101.5" x2="167.9" y2="189.5"/>

      <!--      44line-->
      <line class="st01" x1="296.9" y1="101.5" x2="296.9" y2="189.5"/>


      <!--      &lt;!&ndash;      29line&ndash;&gt;-->
      <!--      <line class="st01" x1="410.9" y1="106.5" x2="410.9" y2="222.5"/>-->

      <!--      45line-->
      <line class="st01" x1="426.9" y1="106.5" x2="426.9" y2="189.5"/>
      <!--      &lt;!&ndash;      30line&ndash;&gt;-->
      <!--      <line class="st01" x1="537.9" y1="106.5" x2="537.9" y2="222.5"/>-->

      <!--      46line-->
      <line class="st01" x1="551.9" y1="106.5" x2="551.9" y2="189.5"/>

      <!--      47line-->
      <line class="st01" x1="668.9" y1="106.5" x2="668.9" y2="189.5"/>

      <!--      48line-->
      <line class="st01" x1="782.9" y1="114.5" x2="782.9" y2="189.5"/>


      <!--      49line-->
      <line class="st01" x1="903.9" y1="111.8" x2="903.9" y2="189.5"/>

      <!--      50line-->
      <line class="st01" x1="1031.2" y1="111.8" x2="1031.2" y2="189.5"/>

      <!--      51line-->
      <line class="st01" x1="1160.6" y1="111.8" x2="1160.6" y2="189.5"/>

      <!--      52line-->
      <line class="st01" x1="1284.6" y1="111.8" x2="1284.6" y2="189.5"/>

      <!--      53line-->
      <line class="st01" x1="1409.4" y1="111.2" x2="1409.4" y2="189.5"/>

      <!--      54line-->
      <line class="st01" x1="1528.6" y1="111.8" x2="1528.6" y2="189.5"/>


      <circle cx="51.1" cy="162.3" r="9"
              :class="params[0].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 44.5986 166.0894)"
        class="st8 st6 st10 animated infinite flash slow"
      >26</text>

      <circle cx="167.9" cy="163" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 161.3487 166.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >43</text>

      <circle cx="297.4" cy="162" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 290.8487 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >44</text>

      <circle cx="426.9" cy="162" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 420.3488 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >45</text>


      <!--      第10个-->
      <circle cx="551.9" cy="162" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 545.3488 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >46</text>

      <circle cx="668.9" cy="162" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 662.3488 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >47</text>

      <circle cx="782.9" cy="162" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 776.3489 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >48</text>

      <circle cx="903.9" cy="162" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 897.3488 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >49</text>

      <circle cx="1031.2" cy="162" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 1025.2261 165.6665)"
        class="st8 st6 st10 animated infinite flash slow"
      >50</text>

      <!--      第20个-->
      <circle cx="1160.9" cy="162.5" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 1154.3485 166.1665)"
        class="st8 st6 st10 animated infinite flash slow"
      >51</text>

      <circle cx="1285.1" cy="162" r="9" class="st2 animated infinite flash slow"/>
      <text
        transform="matrix(1 0 0 1 1278.5548 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >52</text>

      <circle cx="1409.4" cy="161.8" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 1402.8485 165.5894)"
        class="st8 st6 st10 animated infinite flash slow"
      >53</text>

      <circle cx="1528.6" cy="162" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 1522.0784 165.7561)"
        class="st8 st6 st10 animated infinite flash slow"
      >54</text>
      <!--      第一排止-->


      <!--中右12条飞机线路-->
      <!--      第一架飞机第一条线line-->
      <line
        :class="params[43].view ? 'st011':'st1'"
        x1="1129.6" y1="273.5" x2="1396" y2="273.5"/>
      <circle cx="1242.4" cy="273.5" r="9"
              :class="params[43].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1236.4 277.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >92</text>

      <!--      第一架飞机第二条线path-->
      <path
        :class="params[44].view ? 'st011':'st1'"
        d="M1396.2 309.8h-188.6c-3,0-5.5-2.5-5.5-5.5v-29.5q-0.5,-0.5 -1,-1H1129.6"/>
      <circle cx="1242.4" cy="307.8" r="9"
              :class="params[44].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1236.4 311.8)"
        class="st8 st6 st10 animated infinite flash slow"
      >93</text>

      <!--      第一架飞机第三条线加转弯path-->
      <path
        :class="params[45].view ? 'st011':'st1'"
        d="M1396,344.1h-188.6c-3,0-5.5-2.5-5.5-5.5v-64.1q-0.5,-0.5 -1,-1H1129.6"/>
      <circle cx="1242.4" cy="344.1" r="9"
              :class="params[45].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1236.4 348.1)"
        class="st8 st6 st10 animated infinite flash slow"
      >94</text>

      <!--      第二架飞机第一条线 多段线polyline-->
      <polyline
        :class="params[46].view ? 'st011':'st1'"
        points="1129.6,394.5 1201.9,394.5 1395.5,394.5 "/>
      <circle cx="1242.4" cy="394.5" r="9"
              :class="params[46].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1236.4 398.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >95</text>

      <!--      第二架飞机第二条线path-->
      <path
        :class="params[47].view ? 'st011':'st1'"
        d="M1396 426.1h-183.6c-3,0-5.5-2.5-5.5-5.5v-24q-0.5,-0.5 -1,-1H1129.6"/>
      <circle cx="1242.4" cy="426" r="9"
              :class="params[47].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1236.4 430)"
        class="st8 st6 st10 animated infinite flash slow"
      >96</text>

      <!--      第二架飞机第三条线加转弯path-->
      <path
        :class="params[48].view ? 'st011':'st1'"
        d="M1396,457.6h-184.1c-2.8,0-5-2.2-5-5v-56.2q-0.5,-0.5 -1,-1H1129.6"/>
      <circle cx="1242.4" cy="456.7" r="9"
              :class="params[48].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1236.4 461.7)"
        class="st8 st6 st10 animated infinite flash slow"
      >97</text>


      <!--      第三架飞机第一条直线line-->
      <line
        :class="params[49].view ? 'st011':'st1'"
        x1="1129.6" y1="513" x2="1396" y2="513"/>
      <circle cx="1239.8" cy="513" r="9"
              :class="params[49].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1233.4 517)"
        class="st8 st6 st10 animated infinite flash slow"
      >98</text>

      <!--      第三架飞机第二条线path-->
      <path
        :class="params[50].view ? 'st011':'st1'"
        d="M1395.9 546.2h-192.6c-3,0-5.5-2.5-5.5-5.5v-26.3q-0.5,-0.5 -1,-1H1129.6"/>
      <circle cx="1239.8" cy="545.2" r="9"
              :class="params[50].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1233.6 549.2)"
        class="st8 st6 st10 animated infinite flash slow"
      >99</text>

      <!--      第三架飞机第三条线加转弯path-->
      <path
        :class="params[51].view ? 'st011':'st1'"
        d="M1395.9,578.5h-193c-2.9,0-5.2-2.3-5.2-5.2V513H1129.6"/>
      <circle cx="1239.8" cy="578.5" r="9"
              :class="params[51].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1229.5 582.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >100</text>


      <!--      第四架飞机第一条直线line-->
      <line
        :class="params[52].view ? 'st011':'st1'"
        x1="1129.6" y1="625.5" x2="1395.9" y2="625.5"/>
      <circle cx="1239.8" cy="625.5" r="9"
              :class="params[52].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1230 629.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >101</text>

      <!--      第四架飞机第二条直线line-->
      <path
        :class="params[53].view ? 'st011':'st1'"
        d="M1395.9 657.8h-197.6c-3,0-5.5-2.5-5.5-5.5v-25.5q-0.5,-0.5 -1,-1H1129.6"/>
      <circle cx="1239.8" cy="657.8" r="9"
              :class="params[53].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1230.8 661.8)"
        class="st8 st6 st10 animated infinite flash slow"
      >102</text>

      <!--      第四架飞机第三条线加转弯path-->
      <path
        :class="params[54].view ? 'st011':'st1'"
        d="M1395.9,690h-197.6c-3.1,0-5.7-2.5-5.7-5.7v-58.8H1129.6"/>
      <circle cx="1239.8" cy="690" r="9"
              :class="params[54].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"
      />
      <text
        transform="matrix(1 0 0 1 1230.4 694.3)"
        class="st8 st6 st10 animated infinite flash slow"
      >103</text>


      <!--      最后一排倒数第1个-->
      <circle cx="1030.9" cy="929.6" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 1024.3491 933.3398)"
        class="st8 st6 st10 animated infinite flash slow"
      >23</text>
      <circle cx="996.4" cy="929.6" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 989.8486 933.0476)"
        class="st8 st6 st10 animated infinite flash slow"
      >22</text>
      <circle cx="966.2" cy="929.8" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 959.6823 933.5065)"
        class="st8 st6 st10 animated infinite flash slow"
      >21</text>


      <!--      最后一排倒数第10个-->


      <circle cx="828.7" cy="929.6" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 822.1396 933.3394)"
        class="st8 st6 st10 animated infinite flash slow"
      >18</text>
      <circle cx="794.2" cy="929.6" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 787.6823 933.3392)"
        class="st8 st6 st10 animated infinite flash slow"
      >17</text>


      <circle cx="649.2" cy="929.4" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 642.6819 933.1738)"
        class="st8 st6 st10 animated infinite flash slow"
      >14</text>
      <circle cx="616.4" cy="929.4" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 609.8488 933.173)"
        class="st8 st6 st10 animated infinite flash slow"
      >13</text>
      <circle cx="586.9" cy="929.6" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 580.3487 933.3397)"
        class="st8 st6 st10 animated infinite flash slow"
      >12</text>
      <circle cx="555.9" cy="929.6" r="9" class="st2 animated flash infinite slow"/>
      <text
        transform="matrix(1 0 0 1 549.3486 934.0579)"
        class="st8 st6 st10 animated infinite flash slow"
      >11</text>


      <!--      右侧加油单元24条线路-->
      <!--      加油单元24个控制阀-->
      <!--      1单元左-->
      <path
        :class="params[15].view ? 'st011':'st1'"
        d="M1622.6,85.5h54.2l-0.1-26.2c0-2.3,1.8-4.1,4.1-4.1h137.9"/>
      <circle cx="1743.35" cy="55.1" r="9"
              :class="params[15].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 59.1)"
        class="st8 st6 st10 animated infinite flash slow"
      >68</text>
      <!--      1单元右-->
      <path
        :class="params[16].view ? 'st011':'st1'"
        d="M1622.6,85.5h54.2l0.1,15.2c0,2.7,2.2,4.8,4.8,4.8h136.9"/>
      <circle cx="1743.35" cy="106.5" r="9"
              :class="params[16].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 110)"
        class="st8 st6 st10 animated infinite flash slow"
      >69</text>
      <!--      2单元左-->
      <path
        :class="params[17].view ? 'st011':'st1'"
        d="M1622.6,166.4h54.2l-0.1-22.2c0-2.3,1.8-4.1,4.1-4.1h137.9"/>
      <circle cx="1743.35" cy="139.4" r="9"
              :class="params[17].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 143.4)"
        class="st8 st6 st10 animated infinite flash slow"
      >70</text>
      <!--      2单元右-->
      <path
        :class="params[18].view ? 'st011':'st1'"
        d="M1622.6,166.4h54.2l0.1,20.2c0,2.7,2.2,4.8,4.8,4.8h136.5"/>
      <circle cx="1743.35" cy="190.4" r="9"
              :class="params[18].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 194.4)"
        class="st8 st6 st10 animated infinite flash slow"
      >71</text>
      <!--      3单元左-->
      <path
        :class="params[19].view ? 'st011':'st1'"
        d="M1622.6,247h55.1v-22.5c0-2.4,1.9-4.3,4.3-4.3h136"/>
      <circle cx="1743.35" cy="220" r="9"
              :class="params[19].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 224)"
        class="st8 st6 st10 animated infinite flash slow"
      >72</text>
      <!--      3单元右-->
      <path
        :class="params[20].view ? 'st011':'st1'"
        d="M1622.6,247h55.1v22.5c0,2.7,2.2,4.8,4.8,4.8h136.5"/>
      <circle cx="1743.35" cy="273" r="9"
              :class="params[20].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 277)"
        class="st8 st6 st10 animated infinite flash slow"
      >73</text>
      <!--      4单元左-->
      <path
        :class="params[21].view ? 'st011':'st1'"
        d="M1623.2,334.7h56.7V313c0-2.3,1.9-4.1,4.1-4.1h137"/>
      <circle cx="1743.35" cy="308.5" r="9"
              :class="params[21].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 312.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >74</text>
      <!--      4单元右-->
      <path
        :class="params[22].view ? 'st011':'st1'"
        d="M1623.2,334.7h56.7v21.4c0,2.3,1.9,4.2,4.2,4.2h137.1"/>
      <circle cx="1743.35" cy="360.5" r="9"
              :class="params[22].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 364.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >75</text>
      <!--      5单元左-->
      <path
        :class="params[23].view ? 'st011':'st1'"
        d="M1622.6,421.3h54.7v-25.5c0-0.7,0.6-1.3,1.3-1.3l139.5,0"/>
      <circle cx="1743.35" cy="394.3" r="9"
              :class="params[23].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 398.3)"
        class="st8 st6 st10 animated infinite flash slow"
      >76</text>
      <!--      5单元右-->
      <path
        :class="params[24].view ? 'st011':'st1'"
        d="M1622.6,421.3h54.7v21.7c0,0.8,0.7,1.5,1.5,1.5h139.4"/>
      <circle cx="1743.35" cy="446.3" r="9"
              :class="params[24].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 450.3)"
        class="st8 st6 st10 animated infinite flash slow"
      >77</text>
      <!--      6单元左-->
      <path
        :class="params[25].view ? 'st011':'st1'"
        d="M1622.6,506.5h54.7v-26.7c0-0.9,0.7-1.6,1.6-1.6h139.2"/>
      <circle cx="1743.35" cy="479.5" r="9"
              :class="params[25].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 483.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >78</text>
      <!--      6单元右-->
      <path
        :class="params[26].view ? 'st011':'st1'"
        d="M1622.6,506.5h54.7v20.9c0,1,0.8,1.7,1.7,1.7h139.1"/>
      <circle cx="1743.35" cy="531.5" r="9"
              :class="params[26].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 535.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >79</text>
      <!--      7单元左-->
      <path
        :class="params[27].view ? 'st011':'st1'"
        d="M1623.2,592.8h57V567c0-1,0.8-1.8,1.8-1.8h139"/>
      <circle cx="1743.35" cy="565.8" r="9"
              :class="params[27].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 569.8)"
        class="st8 st6 st10 animated infinite flash slow"
      >80</text>
      <!--      7单元右-->
      <path
        :class="params[28].view ? 'st011':'st1'"
        d="M1623.2,592.8h57v21.7c0,1,0.8,1.8,1.8,1.8h139"/>
      <circle cx="1743.35" cy="617.8" r="9"
              :class="params[28].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 621.8)"
        class="st8 st6 st10 animated infinite flash slow"
      >81</text>
      <!--      8单元左-->
      <path
        :class="params[29].view ? 'st011':'st1'"
        d="M1623.2,677.5h57v-24.3c0-0.6,0.5-1.2,1.2-1.2h139.7"/>
      <circle cx="1743.35" cy="650.5" r="9"
              :class="params[29].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 654.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >82</text>
      <!--      8单元右-->
      <path
        :class="params[30].view ? 'st011':'st1'"
        d="M1623.2,677.5h57v19.6c0,0.8,0.6,1.4,1.4,1.4h139.4"/>
      <circle cx="1743.35" cy="698.5" r="9"
              :class="params[30].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 701.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >83</text>
      <!--      9单元左-->
      <path
        :class="params[31].view ? 'st011':'st1'"
        d="M1623.2,762.5h57v-24.7c0-0.8,0.7-1.5,1.5-1.5h139.3"/>
      <circle cx="1743.35" cy="736.3" r="9"
              :class="params[31].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 740.3)"
        class="st8 st6 st10 animated infinite flash slow"
      >84</text>
      <!--      9单元右-->
      <path
        :class="params[32].view ? 'st011':'st1'"
        d="M1623.2,762.5h57v19.5c0,1,0.8,1.7,1.7,1.7h139.1"/>
      <circle cx="1743.35" cy="784.3" r="9"
              :class="params[32].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 789.3)"
        class="st8 st6 st10 animated infinite flash slow"
      >85</text>
      <!--      10单元左-->
      <path
        :class="params[33].view ? 'st011':'st1'"
        d="M1622.2,847.8h54.7v-21.5c0-0.7,0.6-1.3,1.3-1.3h139.5"/>
      <circle cx="1743.359" cy="825.8" r="9"
              :class="params[33].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 829.8)"
        class="st8 st6 st10 animated infinite flash slow"
      >86</text>
      <!--      10单元右-->
      <path
        :class="params[34].view ? 'st011':'st1'"
        d="M1622.2,847.8h54.7v19.3c0,0.9,0.7,1.7,1.7,1.7h139.2"/>
      <circle cx="1743.359" cy="870.8" r="9"
              :class="params[34].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 874.8)"
        class="st8 st6 st10 animated infinite flash slow"
      >87</text>
      <!--      11单元左-->
      <path
        :class="params[35].view ? 'st011':'st1'"
        d="M1622.2,932h54.7v-19.6c0-2.3,1.9-4.1,4.1-4.1h136.7"/>
      <circle cx="1743.35" cy="909" r="9"
              :class="params[35].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 913)"
        class="st8 st6 st10 animated infinite flash slow"
      >88</text>
      <!--      11单元右-->
      <path
        :class="params[36].view ? 'st011':'st1'"
        d="M1622.2,932h54.7v16c0,2.6,2.1,4.8,4.8,4.8h136.1"/>
      <circle cx="1743.35" cy="955" r="9"
              :class="params[36].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 959)"
        class="st8 st6 st10 animated infinite flash slow"
      >89</text>
      <!--      12单元左-->
      <path
        :class="params[37].view ? 'st011':'st1'"
        d="M1622,1016.5h54.7V999c0-2.4,1.9-4.3,4.3-4.3h136.6"/>
      <circle cx="1743.35" cy="994.5" r="9"
              :class="params[37].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 999.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >90</text>
      <!--      12单元右-->
      <path
        :class="params[38].view ? 'st011':'st1'"
        d="M1622,1016.5h54.7v16c0,2.5,2,4.5,4.5,4.5h136.3"/>
      <circle cx="1743.35" cy="1038.5" r="9"
              :class="params[38].view ? 'st4 animated flash infinite slow' : 'st2 animated flash infinite slow'"/>
      <text
        transform="matrix(1 0 0 1 1737.35 1042.5)"
        class="st8 st6 st10 animated infinite flash slow"
      >91</text>

      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 78.543 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 203.8136 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 334.3136 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 456.9785 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 570.9786 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 689.8531 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 815.0198 64.5761)"
      />

      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 1194.7178 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 -37.9802 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="119"
        height="116"
        xlink:href="@/image/fa4.png"
        transform="matrix(0.8004 0 0 0.8004 1005.5593 984.3333)"
      />
      <image
        style="overflow:visible;"
        width="119"
        height="116"
        xlink:href="@/image/fa4.png"
        transform="matrix(0.8004 0 0 0.8004 836.8926 985.1512)"
      />
      <image
        style="overflow:visible;"
        width="119"
        height="116"
        xlink:href="@/image/fa4.png"
        transform="matrix(0.8004 0 0 0.8004 634.2676 985.3333)"
      />
      <image
        style="overflow:visible;"
        width="119"
        height="116"
        xlink:href="@/image/fa4.png"
        transform="matrix(0.8004 0 0 0.8004 389.601 984.75)"
      />
      <image
        style="overflow:visible;"
        width="119"
        height="116"
        xlink:href="@/image/fa4.png"
        transform="matrix(0.8004 0 0 0.8004 64.601 984.75)"
      />
      <image
        style="overflow:visible;"
        width="119"
        height="116"
        xlink:href="@/image/fa4.png"
        transform="matrix(0.651 0 0 0.651 1205.8926 998)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 1431.5825 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 1316.543 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 1070.0198 64.5761)"
      />
      <image
        style="overflow:visible;"
        width="960"
        height="540"
        xlink:href="@/image/fa5.png"
        transform="matrix(0.2133 -2.664174e-02 1.905677e-02 0.1526 940.6865 64.5761)"
      />


      <text transform="matrix(1 0 0 1 1844.5415 61.5732)" class="st6 st7">1单元左</text>
      <text transform="matrix(1 0 0 1 1844.5415 104.149)" class="st6 st7">1单元右</text>
      <text transform="matrix(1 0 0 1 1844.5415 146.7253)" class="st6 st7">2单元左</text>
      <text transform="matrix(1 0 0 1 1844.7087 189.3012)" class="st6 st7">2单元右</text>
      <text transform="matrix(1 0 0 1 1844.7087 231.8772)" class="st6 st7">3单元左</text>
      <text transform="matrix(1 0 0 1 1844.7087 274.4536)" class="st6 st7">3单元右</text>
      <text transform="matrix(1 0 0 1 1844.7087 317.0293)" class="st6 st7">4单元左</text>
      <text transform="matrix(1 0 0 1 1844.7087 359.6057)" class="st6 st7">4单元右</text>
      <text transform="matrix(1 0 0 1 1844.7085 402.1818)" class="st6 st7">5单元左</text>
      <text transform="matrix(1 0 0 1 1844.7085 444.7578)" class="st6 st7">5单元右</text>
      <text transform="matrix(1 0 0 1 1844.7085 487.3338)" class="st6 st7">6单元左</text>
      <text transform="matrix(1 0 0 1 1844.7085 529.9099)" class="st6 st7">6单元右</text>
      <text transform="matrix(1 0 0 1 1844.7085 572.4862)" class="st6 st7">7单元左</text>
      <text transform="matrix(1 0 0 1 1844.7085 615.062)" class="st6 st7">7单元右</text>
      <text transform="matrix(1 0 0 1 1844.7085 657.6381)" class="st6 st7">8单元左</text>
      <text transform="matrix(1 0 0 1 1844.7085 700.2145)" class="st6 st7">8单元右</text>
      <text transform="matrix(1 0 0 1 1844.7085 742.7905)" class="st6 st7">9单元左</text>
      <text transform="matrix(1 0 0 1 1844.7085 785.3665)" class="st6 st7">9单元右</text>
      <text transform="matrix(1 0 0 1 1839.8574 827.9424)" class="st6 st7">10单元左</text>
      <text transform="matrix(1 0 0 1 1839.8574 870.5186)" class="st6 st7">10单元右</text>
      <text transform="matrix(1 0 0 1 1839.8574 913.095)" class="st6 st7">11单元左</text>
      <text transform="matrix(1 0 0 1 1839.8574 955.671)" class="st6 st7">11单元右</text>
      <text transform="matrix(1 0 0 1 1839.8574 998.2469)" class="st6 st7">12单元左</text>
      <text transform="matrix(1 0 0 1 1839.8574 1040.823)" class="st6 st7">12单元右</text>
      <image
        style="overflow:visible;"
        width="804"
        height="515"
        xlink:href="@/image/fa3.png"
        transform="matrix(0.2427 0 0 0.2427 533.8087 321.537)"
      />
      <image
        style="overflow:visible;"
        width="804"
        height="515"
        xlink:href="@/image/fa3.png"
        transform="matrix(0.2427 0 0 0.2427 533.8087 432.8703)"
      />
      <circle class="st8" cx="36.7" cy="101.3" r="11"/>
      <text transform="matrix(0.8431 0 0 1 27.7305 107.9665)" class="st6 st9">33</text>
      <circle class="st8" cx="153" cy="101" r="11"/>
      <text transform="matrix(0.8431 0 0 1 143.0344 107.6193)" class="st6 st9">34</text>
      <circle class="st8" cx="279" cy="101" r="11"/>
      <text transform="matrix(0.8431 0 0 1 270.0344 107.6193)" class="st6 st9">35</text>
      <circle class="st8" cx="410.9" cy="101" r="11"/>
      <text transform="matrix(0.8431 0 0 1 401.8824 107.6193)" class="st6 st9">36</text>
      <circle class="st8" cx="530.9" cy="101.3" r="11"/>
      <text transform="matrix(0.8431 0 0 1 521.8824 107.9822)" class="st6 st9">37</text>
      <circle class="st8" cx="645" cy="101.3" r="11"/>
      <text transform="matrix(0.8431 0 0 1 635.9898 107.9822)" class="st6 st9">38</text>
      <circle class="st8" cx="765" cy="101.3" r="11"/>
      <text transform="matrix(0.8431 0 0 1 756.0344 107.9822)" class="st6 st9">39</text>
      <circle class="st8" cx="889.1" cy="101.3" r="11"/>
      <text transform="matrix(0.8431 0 0 1 880.0638 107.9822)" class="st6 st9">40</text>
      <circle class="st8" cx="1014.1" cy="101.3" r="11"/>
      <text transform="matrix(0.8431 0 0 1 1005.0638 107.9822)" class="st6 st9">41</text>
      <circle class="st8" cx="1144.3" cy="101.1" r="11"/>
      <text transform="matrix(0.8431 0 0 1 1135.3138 107.7322)" class="st6 st9">42</text>
      <circle class="st8" cx="1270.3" cy="100.9" r="11"/>
      <text transform="matrix(0.8431 0 0 1 1261.3138 107.5508)" class="st6 st9">43</text>
      <circle class="st8" cx="1391.1" cy="100.6" r="11"/>
      <text transform="matrix(0.8431 0 0 1 1382.0638 107.2534)" class="st6 st9">44</text>
      <circle class="st8" cx="581.2" cy="362.4" r="11"/>
      <text transform="matrix(0.8431 0 0 1 572.2305 369.0568)" class="st6 st9">46</text>
      <circle class="st8" cx="579.4" cy="475.3" r="11"/>
      <text transform="matrix(0.8431 0 0 1 570.3972 481.9395)" class="st6 st9">47</text>
      <circle class="st8" cx="1505.4" cy="101.3" r="11"/>
      <text transform="matrix(0.8431 0 0 1 1496.3677 107.8875)" class="st6 st9">45</text>

      <g>
        <rect x="127" y="46" class="st11" width="5" height="80"/>
        <rect x="127.5" y="75.3" class="st12" width="4" height="49.6"/>
      </g>
      <g>
        <rect x="383.5" y="46" class="st11" width="5" height="80"/>
        <rect x="384" y="75.3" class="st12" width="4" height="49.6"/>
      </g>
      <g>
        <rect x="623" y="46.5" class="st11" width="5" height="80"/>
        <rect x="623.5" y="75.9" class="st12" width="4" height="49.6"/>
      </g>
      <g>
        <rect x="716.8" y="287.7" class="st11" width="5" height="80"/>
        <rect x="717.3" y="317.1" class="st12" width="4" height="49.6"/>
      </g>
      <g>
        <rect x="989" y="46.5" class="st11" width="5" height="80"/>
        <rect x="989.5" y="75.9" class="st12" width="4" height="49.6"/>
      </g>
      <g>
        <rect x="1366.3" y="46.8" class="st11" width="5" height="80"/>
        <rect x="1366.8" y="76.2" class="st12" width="4" height="49.6"/>
      </g>
      <text transform="matrix(1 0 0 1 61 50)" class="st13 st6 st10">1132.78mm</text>
      <text transform="matrix(1 0 0 1 396.6819 52)" class="st13 st6 st10">1757.58mm</text>
      <text transform="matrix(1 0 0 1 637.9898 52)" class="st13 st6 st10">1486.18mm</text>
      <text transform="matrix(1 0 0 1 1010.015 52)" class="st13 st6 st10">1486.18mm</text>
      <text transform="matrix(1 0 0 1 1382.2263 52)" class="st13 st6 st10">1486.18mm</text>
      <text transform="matrix(1 0 0 1 648.8927 310.7773)" class="st13 st6 st10">1486.18mm</text>
      <text transform="matrix(1 0 0 1 1531.5593 182.0293)" class="st22">来自油耗库</text>
      <text transform="matrix(1 0 0 1 511.5593 317.0293)" class="st15 st6 st9">沉淀罐</text>
      <text transform="matrix(1 0 0 1 531.5593 627.0293)" class="st15 st6 st9">泵房</text>
      <text transform="matrix(1 0 0 1 81.5593 987.0293)" class="st15 st6 st9">1#油泵</text>
      <text transform="matrix(1 0 0 1 412.5593 987.0293)" class="st15 st6 st9">2#油泵</text>
      <text transform="matrix(1 0 0 1 658.5593 987.0293)" class="st15 st6 st9">3#油泵</text>
      <text transform="matrix(1 0 0 1 858.5593 987.0293)" class="st15 st6 st9">4#油泵</text>
      <text transform="matrix(1 0 0 1 1031.5593 987.0293)" class="st15 st6 st9">5#油泵</text>
      <text transform="matrix(1 0 0 1 1218.5593 995.0293)" class="st15 st6 st9">抽底泵</text>
      <text transform="matrix(1 0 0 1 1104.3494 350.1852)" class="st15 st6 st16 st17 st023">小环路</text>
      <text transform="matrix(1 0 0 1 1611.6666 138.5002)" class="st18 st6 st19 st20">大一环路</text>
      <text transform="matrix(1 0 0 1 1608.6664 397.5002)" class="st18 st6 st19 st20">大二环路</text>
      <text transform="matrix(1 0 0 1 1568.6664 497.5002)" class="st15 st6 st19 st020">加油单元</text>
      <text transform="matrix(1 0 0 1 1608.6666 642.8335)" class="st18 st6 st19 st20">大三环路</text>
      <text transform="matrix(1 0 0 1 1608.6664 882.4452)" class="st18 st6 st19 st20">大四环路</text>
      <image
        style="overflow:visible;"
        width="572"
        height="841"
        xlink:href="@/image/plane.png"
        transform="matrix(0.1134 0 0 0.1134 1399.6085 266.3116)"
      />
      <image
        style="overflow:visible;"
        width="572"
        height="841"
        xlink:href="@/image/plane.png"
        transform="matrix(0.1134 0 0 0.1134 1399.6085 382.2959)"
      />
      <image
        style="overflow:visible;"
        width="572"
        height="841"
        xlink:href="@/image/plane.png"
        transform="matrix(0.1134 0 0 0.1134 1399.6085 502.2803)"
      />
      <image
        style="overflow:visible;"
        width="572"
        height="841"
        xlink:href="@/image/plane.png"
        transform="matrix(0.1134 0 0 0.1134 1399.6085 612.2803)"
      />
    </svg>
      <div class="box-card" shadow="never">
            <div slot="header" class="clearfix">
              <span>当前设备</span>
            </div>
            <div class="text item">
              <ul class="tab">
                <li>
                  <span>设备名称:</span>33号油罐
                </li>
                <li>
                  <span>运行状态:</span>正常运行
                </li>
                <li>
                  <span>预警状态:</span>无
                </li>
              </ul>
            </div>
          </div>
  </div>
</template>

<script>
export default {
    data(){
      return {
        infoList:[],
        params: [
          {view: false, num: 26},
          {view: false, num: 27},
          {view: false, num: 28},
          {view: false, num: 29},
          {view: true, num: 30},
          {view: true, num: 31},
          {view: false, num: 32},

          {view: true, num: 4},
          {view: true, num: 9},
          {view: true, num: 15},
          {view: true, num: 19},
          {view: true, num: 24},

          {view: true, num: 2},
          {view: true, num: 1},
          {view: true, num: 3},

          {view: true, num: 68},
          {view: true, num: 69},
          {view: true, num: 70},
          {view: true, num: 71},
          {view: true, num: 72},
          {view: true, num: 73},
          {view: true, num: 74},
          {view: true, num: 75},
          {view: true, num: 76},
          {view: true, num: 77},
          {view: true, num: 78},
          {view: true, num: 79},
          {view: true, num: 80},
          {view: true, num: 81},
          {view: true, num: 82},
          {view: true, num: 83},
          {view: true, num: 84},
          {view: true, num: 85},
          {view: true, num: 86},
          {view: true, num: 87},
          {view: true, num: 88},
          {view: true, num: 89},
          {view: true, num: 90},
          {view: true, num: 91},

          {view: true, num: 42},
          {view: true, num: 39},
          {view: true, num: 40},
          {view: true, num: 41},

          {view: false, num: 92},
          {view: true, num: 93},
          {view: true, num: 94},
          {view: true, num: 95},
          {view: true, num: 96},
          {view: true, num: 97},
          {view: true, num: 98},
          {view: true, num: 99},
          {view: false, num: 100},
          {view: true, num: 101},
          {view: true, num: 102},
          {view: false, num: 103},

          {view: true, num: 33},
          {view: true, num: 34},
          {view: true, num: 35},
          {view: true, num: 36},
          {view: true, num: 37},
          {view: true, num: 38},

          {view: true, num: 5},
          {view: true, num: 10},
          {view: false, num: 16},
          {view: true, num: 20},
          {view: true, num: 25},

          {view: true, num: 104},
        ]
      }
    },
    beforeDestroy() {
    },
    created() {
      this.initWebSocket()
    },
    methods:{
      initWebSocket() {
        if (typeof (WebSocket) !== 'undefined') {
          this.websock = new WebSocket(process.env.VUE_APP_WS_API + 'ws/depot/130')
          console.log(process.env.VUE_APP_WS_API + '/ws/depot/6')
          this.websock.onmessage = this.websocketonmessage
          this.websock.onopen = this.websocketonopen
          this.websock.onerror = this.websocketonerror
          this.websock.onclose = this.websocketclose
        }
      },
      websocketonopen() { // 连接建立之后执行send方法发送数据
        console.log('websocketonopen')
      },
      websocketonerror() { // 连接建立失败重连
        this.initWebSocket()
      },
      websocketonmessage(r) {
        console.log('数据接收')
        console.log(r)
        if (r.data === '连接成功') {
          return
        }
        if (r.data) {
          var data= JSON.parse(r.data);
          this.infoList = data 
          console.log(data)
        }
      },
      websocketsend(Data) { // 数据发送
        this.websock.send(Data)
      },
      websocketclose(e) { // 关闭
        console.log('断开连接', e)
      }
    }
}
</script>

<style scoped>
.st0 {
    fill: #a6997c;
  }

  .st1 {
    fill: none;
    stroke: #dcdfe6;
    stroke-width: 5;
    stroke-miterlimit: 10;
    opacity: 0.3
  }

  .st01 {
    fill: none;
    stroke: #67c23a;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st02 {
    fill: none;
    stroke: #ff0000;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st03 {
    fill: none;
    stroke: #8d5d0a;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st04 {
    fill: none;
    stroke: #b26bbf;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st05 {
    fill: none;
    stroke: #1a0306;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st06 {
    fill: none;
    stroke: #d0a513;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st07 {
    fill: none;
    stroke: #6bd013;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st08 {
    fill: none;
    stroke: #0eac87;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st09 {
    fill: none;
    stroke: #f8d105;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st010 {
    fill: none;
    stroke: #05f88f;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st011 {
    fill: none;
    stroke: #0aa5a5;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st001 {
    fill: none;
    stroke: #6bbf6c;
    stroke-width: 5;
    stroke-miterlimit: 10;
  }

  .st2 {
    fill: #ff0000;
  }

  .st3 {
    fill: #f40606;
  }

  .st4 {
    fill: #67c23a;
  }

  .st5 {
    fill: #67c23a;
  }

  .st6 {
    font-family: "AdobeSongStd-Light-GBpc-EUC-H";
    font-weight: bold;
  }

  .st7 {
    font-size: 21px;
  }

  .st8 {
    fill: #ffffff;
  }

  .st9 {
    font-size: 17.9998px;
  }

  .st10 {
    font-size: 10px;
  }

  .st11 {
    fill: #7a7a7a;
  }

  .st12 {
    fill: #67c23a;
  }

  /* .st13{fill:#03EDFF;} */
  .st13 {
    fill: #ffffff;
  }

  /* .st14{fill:#FF4217;} */
  .st14 {
    fill: #ffffff;
  }

  .st15 {
    fill: #d02c00;
  }

  /* .st15{fill:white;} */
  .st16 {
    font-size: 21px;
    glyph-orientation-vertical: 0;
    writing-mode: tb;
  }

  .st17 {
    letter-spacing: 168;
  }

  /* .st18{fill:#D02600;} */
  .st18 {
    fill: #0aa5a5;
  }

  .st19 {
    font-size: 14px;
    glyph-orientation-vertical: 0;
    writing-mode: tb;
    font-weight: bold;
  }

  /*.st20 {*/
  /*  letter-spacing: 1px;*/
  /*}*/

  .st020 {
    letter-spacing: 25px;
  }

  .st21 {
    fill: #fbb814;
  }

  .st22 {
    font-size: 13px;
    fill: red;
  }

  .st023 {
    letter-spacing: 85px;
  }

  svg {
    margin: 0 20px;
  }
  .box-card {
    width:250px;
    height:200px;
    position:absolute;
    right:0px;
    bottom:0px;
    border:1px solid #1d4e7f;
     background:rgba(18,50,86,0.7);
     color:white;
     border-bottom-right-radius:25px;
  }
  .clearfix {
    background:#047bd0;
    line-height:40px;
    padding-left:20px;
    font-size:18px;
  }
  .tab,.tab li {
    margin:0;
    padding:0;
    list-style:none;
    font-size:16px;
    line-height:40px;
    padding-left:10px;
  }
  .tab{
    padding-top:10px;
  }
  .tab li span {
    padding-right:5px;
  }
</style>