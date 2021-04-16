<template>
  <div class="tankBox">
     <ul>
        <li v-for="(item,index) in 20" :key="index"></li>
        <li>罐一</li>
      </ul>
      <ul>
        <li v-for="(item,index) in 20" :key="index"></li>
        <li>罐二</li>
      </ul>
      <ul>
        <li v-for="(item,index) in 20" :key="index"></li>
        <li>罐三</li>
      </ul>
      <ul>
        <li v-for="(item,index) in 20" :key="index"></li>
        <li>罐四</li>
      </ul>
  </div>

</template>

<script>
import { mapGetters } from 'vuex'

export default {
  data() {
    return {
      statisticsData: {
        article_count: 1024,
        pageviews_count: 1024
      }
    }
  },
  computed: {
    ...mapGetters([
      'name',
      'avatar',
      'roles'
    ])
  },
  created() {
    this.showTank()
  },
  methods: {
    showTank(){
      let tank = document.getElementsByTagName('li');
    }
  }
}
</script>

<style lang="scss" scoped>
.tankBox {
  display:-webkit-flex;
  display:flex;
  justify-content: space-around;
  padding-top:30px;
}
ul,li {
  margin:0;
  padding:0;
  list-style: none;
}
ul li {
  width:30px;
  height:5px;
  background:#93a3b7;
  margin-bottom:5px;
  border-radius:3px;
}
ul li:last-child {
  font-size:14px;
  width:30px;
  text-align:center;
  line-height:30px;
  background:none;
  color:white;
}
</style>
