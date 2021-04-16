<template>
  <el-carousel trigger="click" height="150px" autoplay="autoplay" indicator-position="outside">
      <el-carousel-item v-for="item in 4" :key="item">
        <tank-bar></tank-bar>
      </el-carousel-item>
    </el-carousel>
</template>

<script>
import TankBar from './component/TankBar'

export default {
  components: { 
      TankBar:TankBar
    },
  props: {
  },
  data() {
    return {
      autoplay:fasle
    }
  },
  mounted() {
  },
  beforeDestroy() {
  },
  methods: {
    
      
    }
}
</script>
