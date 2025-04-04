<script setup lang="ts">
import { ref, reactive, onMounted } from 'vue'
import { useRouter } from 'vue-router'
import md5 from 'js-md5'
import axios from 'axios'

const router = useRouter()

interface FormState {
  username: string
  password: string
  remember: boolean
}

const formState = reactive<FormState>({
  username: '',
  password: '',
  remember: false
})

const loading = ref(false)
const errorMessage = ref('')

// 保存用户凭证到localStorage
const saveCredentials = () => {
  if (formState.remember) {
    // 使用MD5加密处理密码
    const encryptedPassword = md5(formState.password)
    localStorage.setItem('savedUsername', formState.username)
    localStorage.setItem('savedPassword', encryptedPassword)
    localStorage.setItem('rememberMe', 'true')
  } else {
    clearCredentials()
  }
}

// 清除保存的凭证
const clearCredentials = () => {
  localStorage.removeItem('savedUsername')
  localStorage.removeItem('savedPassword')
  localStorage.removeItem('rememberMe')
}

// 加载保存的凭证
const loadCredentials = () => {
  const savedUsername = localStorage.getItem('savedUsername')
  const savedPassword = localStorage.getItem('savedPassword')
  const rememberMe = localStorage.getItem('rememberMe')
  
  if (savedUsername && savedPassword && rememberMe === 'true') {
    formState.username = savedUsername
    // 由于MD5是单向加密，我们不能解密密码
    // 只保存用户名和记住我的状态
    // 在实际项目中，应该将用户输入的密码加密后与savedPassword比较
    formState.password = '' // 清空密码字段，等待用户输入
    formState.remember = true
  }
}

// 组件挂载时加载保存的凭证
onMounted(() => {
  loadCredentials()
})

const handleLogin = () => {
  // 表单验证
  if (!formState.username) {
    errorMessage.value = '请输入用户名'
    return
  }
  if (!formState.password) {
    errorMessage.value = '请输入密码'
    return
  }
  
  // 开始登录请求
  loading.value = true
  errorMessage.value = ''
  
  // 对密码进行MD5加密
  const encryptedPassword = md5(formState.password)
  
  // 调用登录API
  axios.post('http://127.0.0.1:8011/csServer/v1/agile-auth/login', {
    username: formState.username,
    password: encryptedPassword
  }, {
    headers: {
      'Tenant-Id': '000000',
      'Authorization': 'Basic ZXRvdWNoOns3MEY1QUEyRC1EQTRELTQzMGQtOTM5Ri04MjFCQUMyOUM4RTN9',
      'Content-Type': 'application/json;charset=UTF-8'
    }
  })
  .then(response => {
    loading.value = false
    // 登录成功
    saveCredentials() // 保存凭证
    router.push('/home')
  })
  .catch(error => {
    loading.value = false
    // 登录失败，显示错误信息
    if (error.response && error.response.data && error.response.data.message) {
      errorMessage.value = error.response.data.message
    } else {
      errorMessage.value = '登录失败，请检查网络连接或联系管理员'
    }
    console.error('登录错误:', error)
  })
}
</script>

<template>
  <div class="login-container">
    <div class="login-layout">
      <div class="login-banner">
        <div class="logo-area">
          <img src="/vite.svg" alt="Logo" class="logo" />
          <h2>管理系统</h2>
        </div>
        <div class="banner-content">
          <h1>欢迎使用我们的平台</h1>
          <p>高效、安全、专业的管理解决方案</p>
        </div>
      </div>
      <div class="login-box">
        <div class="login-header">
          <h1>欢迎登录</h1>
          <p>登录以继续访问平台</p>
        </div>
        
        <div class="login-form">
          <div class="form-item" :class="{ 'has-error': errorMessage }">
            <div class="input-wrapper">
              <input 
                id="username" 
                type="text" 
                v-model="formState.username" 
                placeholder="用户名/邮箱"
                @focus="errorMessage = ''"
              />
            </div>
          </div>
          
          <div class="form-item">
            <div class="input-wrapper">
              <input 
                id="password" 
                type="password" 
                v-model="formState.password" 
                placeholder="密码"
                @focus="errorMessage = ''"
              />
            </div>
          </div>
          
          <div class="form-item checkbox-item">
            <div class="checkbox-wrapper">
              <input 
                id="remember" 
                type="checkbox" 
                v-model="formState.remember"
              />
              <label for="remember">记住我</label>
            </div>
            <a href="#" class="forgot-link">忘记密码?</a>
          </div>
          
          <div class="error-message" v-if="errorMessage">{{ errorMessage }}</div>
          
          <button 
            class="login-button" 
            @click="handleLogin" 
            :disabled="loading"
          >
            <span v-if="!loading">登录</span>
            <span v-else class="loading-spinner"></span>
          </button>
          
          <div class="login-footer">
            <p>还没有账号? <a href="#">立即注册</a></p>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<style scoped>
.login-container {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100vw;
  height: 100vh;
  background: linear-gradient(135deg, #f5f7fa 0%, #c3cfe2 100%);
  background-size: cover;
  background-position: center;
  background-repeat: no-repeat;
  padding: 0;
  margin: 0;
  box-sizing: border-box;
  overflow: hidden;
  position: fixed;
  top: 0;
  left: 0;
}

.login-layout {
  display: flex;
  width: 90%;
  max-width: 1400px;
  height: auto;
  min-height: 600px;
  max-height: 90vh;
  background-color: #ffffff;
  border-radius: 8px;
  box-shadow: 0 12px 40px rgba(0, 0, 0, 0.15);
  overflow: hidden;
}

.login-banner {
  flex: 1;
  background: linear-gradient(135deg, #667eea 0%, #764ba2 100%);
  color: white;
  padding: 50px 40px;
  display: flex;
  flex-direction: column;
  justify-content: space-between;
}

.logo-area {
  display: flex;
  align-items: center;
  margin-bottom: 40px;
}

.logo {
  width: 40px;
  height: 40px;
  margin-right: 12px;
}

.logo-area h2 {
  font-size: 24px;
  font-weight: 600;
  margin: 0;
}

.banner-content {
  margin-top: auto;
}

.banner-content h1 {
  font-size: 42px;
  font-weight: 700;
  margin-bottom: 20px;
}

.banner-content p {
  font-size: 20px;
  opacity: 0.9;
}

.login-box {
  width: 100%;
  max-width: 500px;
  background-color: #ffffff;
  padding: 50px 40px;
  transition: transform 0.3s ease;
  display: flex;
  flex-direction: column;
  justify-content: center;
}

.login-header {
  text-align: center;
  margin-bottom: 40px;
}

.login-header h1 {
  font-size: 36px;
  color: #333;
  margin-bottom: 16px;
  font-weight: 600;
}

.login-header p {
  color: #666;
  font-size: 20px;
}

.form-item {
  margin-bottom: 24px;
}

label {
  display: block;
  margin-bottom: 10px;
  font-size: 16px;
  color: #333;
  font-weight: 500;
}

.input-wrapper {
  position: relative;
}

input[type="text"],
input[type="password"] {
  width: 100%;
  padding: 18px 20px;
  border: 1px solid #ddd;
  border-radius: 8px;
  font-size: 18px;
  transition: all 0.3s;
  background-color: #f9f9f9;
  height: 56px;
}

input[type="text"]:focus,
input[type="password"]:focus {
  border-color: #646cff;
  outline: none;
  box-shadow: 0 0 0 4px rgba(100, 108, 255, 0.15);
  background-color: #fff;
}

.checkbox-item {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.checkbox-wrapper {
  display: flex;
  align-items: center;
}  

.checkbox-wrapper label {
  font-size: 16px;
  margin-bottom: 0;
}

input[type="checkbox"] {
  margin-right: 8px;
  accent-color: #646cff;
}

.forgot-link {
  font-size: 16px;
  color: #646cff;
}

.login-button {
  width: 100%;
  padding: 18px;
  background-color: #646cff;
  color: white;
  border: none;
  border-radius: 8px;
  font-size: 20px;
  font-weight: 500;
  cursor: pointer;
  transition: all 0.3s;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 60px;
  margin-top: 10px;
}

.login-button:hover {
  background-color: #535bf2;
  transform: translateY(-2px);
  box-shadow: 0 6px 16px rgba(100, 108, 255, 0.3);
}

.login-button:disabled {
  background-color: #a5a6f6;
  cursor: not-allowed;
  transform: none;
  box-shadow: none;
}

.loading-spinner {
  width: 20px;
  height: 20px;
  border: 3px solid rgba(255, 255, 255, 0.3);
  border-radius: 50%;
  border-top-color: white;
  animation: spin 1s ease-in-out infinite;
}

@keyframes spin {
  to { transform: rotate(360deg); }
}

.login-footer {
  margin-top: 32px;
  text-align: center;
  font-size: 16px;
  color: #666;
}

.login-footer a {
  color: #646cff;
  font-weight: 500;
}

.error-message {
  color: #ff4d4f;
  font-size: 16px;
  margin-bottom: 20px;
}

.has-error input {
  border-color: #ff4d4f;
}

/* 响应式设计 */
@media (min-width: 1201px) {
  .login-layout {
    max-width: 1200px;
  }
  
  .login-banner {
    padding: 50px 40px;
  }
}

@media (max-width: 1200px) {
  .login-layout {
    width: 95%;
    max-width: 1100px;
  }
  
  .login-banner {
    padding: 40px 30px;
  }
  
  .banner-content h1 {
    font-size: 36px;
  }
  
  .login-box {
    padding: 40px 30px;
  }
}

@media (max-width: 992px) {
  .login-layout {
    flex-direction: column;
    max-width: 600px;
    min-width: auto;
    width: 95%;
    max-height: none;
    min-height: auto;
  }
  
  .login-box {
    max-width: 100%;
    min-width: auto;
  }
  
  .login-banner {
    padding: 30px;
    text-align: center;
  }
  
  .logo-area {
    justify-content: center;
    margin-bottom: 20px;
  }
  
  .banner-content {
    margin: 20px 0;
  }
  
  .banner-content h1 {
    font-size: 32px;
  }
  
  .banner-content p {
    font-size: 18px;
  }
  
  .login-box {
    padding: 40px 30px;
  }
  
  .login-header h1 {
    font-size: 32px;
  }
  
  .login-header p {
    font-size: 18px;
  }
  
  input[type="text"],
  input[type="password"] {
    padding: 14px 18px;
    height: 52px;
    font-size: 16px;
  }
  
  .login-button {
    padding: 16px;
    font-size: 18px;
    height: 56px;
  }
}

@media (max-width: 768px) {
  .login-container {
    padding: 10px;
  }
  
  .login-layout {
    width: 100%;
    max-width: 100%;
    margin: 0;
  }
  
  .login-banner {
    padding: 30px 20px;
  }
  
  .banner-content h1 {
    font-size: 28px;
  }
  
  .banner-content p {
    font-size: 16px;
  }
  
  .login-box {
    padding: 30px 25px;
  }
  
  .login-header h1 {
    font-size: 28px;
  }
  
  .login-header p {
    font-size: 16px;
  }
  
  .form-item {
    margin-bottom: 24px;
  }
  
  label {
    font-size: 15px;
  }
  
  input[type="text"],
  input[type="password"] {
    padding: 12px 16px;
    height: 48px;
    font-size: 16px;
  }
  
  .login-button {
    padding: 14px;
    font-size: 16px;
    height: 50px;
  }
  
  .login-footer {
    margin-top: 24px;
    font-size: 14px;
  }
}

@media (max-width: 480px) {
  .login-container {
    padding: 0;
  }
  
  .login-layout {
    border-radius: 0;
  }
  
  .login-banner {
    padding: 25px 15px;
  }
  
  .logo-area {
    margin-bottom: 15px;
  }
  
  .logo {
    width: 32px;
    height: 32px;
  }
  
  .logo-area h2 {
    font-size: 20px;
  }
  
  .banner-content h1 {
    font-size: 24px;
    margin-bottom: 10px;
  }
  
  .banner-content p {
    font-size: 14px;
  }
  
  .login-box {
    padding: 25px 20px;
  }
  
  .login-header {
    margin-bottom: 30px;
  }
  
  .login-header h1 {
    font-size: 24px;
  }
  
  .login-header p {
    font-size: 14px;
  }
  
  .form-item {
    margin-bottom: 20px;
  }
  
  label {
    font-size: 14px;
    margin-bottom: 6px;
  }
  
  input[type="text"],
  input[type="password"] {
    padding: 10px 14px;
    height: 42px;
    font-size: 14px;
  }
  
  .checkbox-wrapper label {
    font-size: 14px;
  }
  
  .forgot-link {
    font-size: 14px;
  }
  
  .login-button {
    padding: 12px;
    font-size: 15px;
    height: 46px;
  }
}
</style>