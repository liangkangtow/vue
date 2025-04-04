<script setup lang="ts">
// 不需要导入HelloWorld组件
</script>

<template>
  <!-- 使用router-view显示路由组件 -->
  <router-view />
</template>

<style>
/* 全局样式 */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

body {
  font-family: 'Arial', sans-serif;
  line-height: 1.6;
  color: #333;
}

a {
  color: #646cff;
  text-decoration: none;
}

a:hover {
  color: #535bf2;
  text-decoration: none;
}
</style>
