<template>
  <div class="flex items-center">
    <span class="text-large font-600 mr-3"> Simplified AES </span>
    <el-tag>实验</el-tag>
  </div>
  <el-divider content-position="right">S-AES</el-divider>
  <div aria-label="A complete example of page header">
    <el-descriptions :column="3" size="small" class="mt-4">
      <el-descriptions-item label="姓名"
      >
        <el-tag size="small">夏劲、张佳</el-tag>
      </el-descriptions-item
      >
      <el-descriptions-item label="学号"
      >
        <el-tag size="small">20214966、20214818</el-tag>
      </el-descriptions-item
      >
      <el-descriptions-item label="时间">
        <el-tag size="small">20231023</el-tag>
      </el-descriptions-item>
      <el-descriptions-item label="学院">
        <el-tag size="small">重庆大学-大数据与软件学院</el-tag>
      </el-descriptions-item>
      <el-descriptions-item label="地点"
      ><el-tag size="small">重庆大学（虎溪校区），重庆</el-tag>
      </el-descriptions-item>
    </el-descriptions>
  </div>
  <ShowPage/>
  <Introduction/>
</template>

<script setup lang="ts">
import { ElNotification as notify } from 'element-plus'
import Introduction from "@/components/Introduction.vue";
import ShowPage from "@/components/ShowPage.vue";

const onBack = () => {
  notify('逗你玩的，嘿嘿')
}
</script>
