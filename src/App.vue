<script setup lang="ts">
// Element Plus 设置中文
import zhCn from 'element-plus/lib/locale/lang/zh-cn'
const locale = zhCn
</script>

<template>
  <el-config-provider :locale="locale">
    <router-view></router-view>
  </el-config-provider>
</template>

<style>
body {
  height: 2000px;
}
</style>
