<template>
  <div>
      <el-tabs v-model="activeName" class="demo-tabs" @tab-click="handleClick">
        <el-tab-pane label="首页" name="first"><Header/></el-tab-pane>
        <el-tab-pane label="加密" name="second"><Encrypt/></el-tab-pane>
        <el-tab-pane label="解密" name="third"><Decrypt/></el-tab-pane>
        <el-tab-pane label="中间相遇破解" name="fourth"><MidAttack/></el-tab-pane>
      </el-tabs>
  </div>
</template>
<script lang="ts" setup>
import { ref } from 'vue'
import type { TabsPaneContext } from 'element-plus'
import Header from "@/components/Header.vue";
import Encrypt from "@/components/Encrypt.vue";
import Decrypt from "@/components/Decrypt.vue";
import MidAttack from "@/components/MidAttack.vue";

const activeName = ref('first')

const handleClick = (tab: TabsPaneContext, event: Event) => {
  console.log(tab, event)
}
</script>
<style>
.demo-tabs > .el-tabs__content {
  padding: 32px;
  color: #6b778c;
  font-size: 32px;
  font-weight: 600;
}
</style>
