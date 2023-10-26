<template>
  <el-carousel indicator-position="outside">
    <el-carousel-item v-for="item in items" :key="item">
      <el-image :src="item.url"/>
    </el-carousel-item>
  </el-carousel>
</template>

<style scoped>
.el-carousel__item h3 {
  display: flex;
  color: #475669;
  opacity: 0.75;
  line-height: 300px;
  margin: 0;
}

.el-carousel__item:nth-child(2n) {
  background-color: #99a9bf;
}

.el-carousel__item:nth-child(2n + 1) {
  background-color: #d3dce6;
}
</style>
<script>
import {ref} from "vue";

export default {
  setup(){
    const items=ref([
      {
        url:'https://github.com/Jinlkj/Pictures/blob/main/尝试破解.png?raw=true'
      },
      {
        url:'https://github.com/Jinlkj/Pictures/blob/main/postman.png?raw=true'
      },
      {
        url:'https://github.com/Jinlkj/Pictures/blob/main/加密文字.png?raw=true'
      },
    ])
    return{
      items
    }
  }
}
</script>
