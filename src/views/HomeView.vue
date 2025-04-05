<script setup lang="ts">
// 首页组件
</script>

<template>
  <div class="home-container">
    <h1>欢迎来到首页</h1>
    <p>您已成功登录系统</p>
    <div class="card">
      <p>这是一个简单的首页示例，登录成功后会跳转到此页面。</p>
      <div class="actions">
        <router-link to="/video" class="video-link">进入视频监控系统</router-link>
      </div>
    </div>
  </div>
</template>

<style scoped>
.home-container {
  padding: 40px;
  text-align: center;
}

h1 {
  color: #333;
  margin-bottom: 16px;
}

p {
  color: #666;
  margin-bottom: 24px;
}

.card {
  background-color: #fff;
  border-radius: 8px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  padding: 24px;
  max-width: 600px;
  margin: 0 auto;
}

.actions {
  margin-top: 20px;
  text-align: center;
}

.video-link {
  display: inline-block;
  background-color: #2c8cf4;
  color: white;
  padding: 10px 20px;
  border-radius: 4px;
  text-decoration: none;
  font-weight: 500;
  transition: all 0.3s;
}

.video-link:hover {
  background-color: #1a7ad9;
  transform: translateY(-2px);
  box-shadow: 0 4px 8px rgba(0, 0, 0, 0.1);
}
</style>