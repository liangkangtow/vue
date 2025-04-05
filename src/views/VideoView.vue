<script setup lang="ts">
import { ref, reactive, onMounted, onUnmounted, computed } from 'vue'

// 树形菜单数据结构
interface TreeNode {
  id: string
  name: string
  children?: TreeNode[]
  isExpanded?: boolean
  isCamera?: boolean
  status?: 'online' | 'offline' | 'warning' // 摄像头状态
  lastUpdate?: string // 最后更新时间
}

// 监控点位数据
const treeData = reactive<TreeNode[]>([
  {
    id: '1',
    name: '广东省教育考试院',
    isExpanded: true,
    children: [
      {
        id: '1-1',
        name: '潮州市',
        isExpanded: true,
        children: [
          {
            id: '1-1-1',
            name: '韩山师范学院保密室',
            isExpanded: true,
            children: [
              {
                id: '1-1-1-1',
                name: '44广东韩山师范学院保密室外',
                isCamera: true,
                status: 'online',
                lastUpdate: '2023-06-15 14:30:22'
              },
              {
                id: '1-1-1-2',
                name: '44广东韩山师范学院保密室内1',
                isCamera: true,
                status: 'online',
                lastUpdate: '2023-06-15 14:30:45'
              },
              {
                id: '1-1-1-3',
                name: '44广东韩山师范学院保密室内2',
                isCamera: true,
                status: 'warning',
                lastUpdate: '2023-06-15 14:15:30'
              },
              {
                id: '1-1-1-4',
                name: '44广东韩山师范学院保密室水室',
                isCamera: true,
                status: 'offline',
                lastUpdate: '2023-06-15 13:45:10'
              }
            ]
          }
        ]
      },
      {
        id: '1-2',
        name: '东莞市',
        children: []
      },
      {
        id: '1-3',
        name: '佛山市',
        children: []
      },
      {
        id: '1-4',
        name: '广州市',
        children: []
      },
      {
        id: '1-5',
        name: '河源市',
        children: []
      },
      {
        id: '1-6',
        name: '惠州市',
        children: []
      },
      {
        id: '1-7',
        name: '江门市',
        children: []
      },
      {
        id: '1-8',
        name: '揭阳市',
        children: []
      },
      {
        id: '1-9',
        name: '茂名市',
        children: []
      },
      {
        id: '1-10',
        name: '梅州市',
        children: []
      },
      {
        id: '1-11',
        name: '清远市',
        children: []
      },
      {
        id: '1-12',
        name: '汕头市',
        children: []
      },
      {
        id: '1-13',
        name: '汕尾市',
        children: []
      },
      {
        id: '1-14',
        name: '韶关市',
        children: []
      },
      {
        id: '1-15',
        name: '深圳市',
        children: []
      },
      {
        id: '1-16',
        name: '阳江市',
        children: []
      },
      {
        id: '1-17',
        name: '云浮市',
        children: []
      },
      {
        id: '1-18',
        name: '湛江市',
        children: []
      },
      {
        id: '1-19',
        name: '肇庆市',
        children: []
      },
      {
        id: '1-20',
        name: '中山市',
        children: []
      },
      {
        id: '1-21',
        name: '珠海市',
        children: []
      }
    ]
  }
])

// 视频布局配置
const layoutOptions = [
  { id: 1, name: '单屏', icon: 'layout-1', description: '适合重点监控' },
  { id: 4, name: '四分屏', icon: 'layout-4', description: '标准监控布局' },
  { id: 6, name: '六分屏', icon: 'layout-6', description: '多点监控' },
  { id: 7, name: '七分屏', icon: 'layout-7', description: '主次结合' },
  { id: 8, name: '八分屏', icon: 'layout-8', description: '走廊监控' },
  { id: 9, name: '九分屏', icon: 'layout-9', description: '网格布局' },
  { id: 10, name: '十分屏', icon: 'layout-10', description: '大厅监控' },
  { id: 13, name: '十三分屏', icon: 'layout-13', description: '多区域监控' },
  { id: 16, name: '十六分屏', icon: 'layout-16', description: '全面监控' },
  { id: 25, name: '二十五分屏', icon: 'layout-25', description: '全景监控' }
]

// 当前选中的监控点
const selectedCamera = ref('')

// 当前选中的布局
const selectedLayout = ref(1)

// 搜索关键词
const searchKeyword = ref('')

// 侧边栏是否折叠
const isSidebarCollapsed = ref(false)

// 是否全屏模式
const isFullscreen = ref(false)

// 当前系统时间
const currentTime = ref(new Date().toLocaleString())

// 更新系统时间
setInterval(() => {
  currentTime.value = new Date().toLocaleString()
}, 1000)

// 切换节点展开/折叠状态
const toggleNode = (node: TreeNode) => {
  if (node.children && node.children.length > 0) {
    node.isExpanded = !node.isExpanded
  }
}

// 选择摄像头
const selectCamera = (node: TreeNode) => {
  if (node.isCamera) {
    selectedCamera.value = node.id
    // 在实际应用中，这里会调用视频播放API
    console.log('选择摄像头:', node.name)
  }
}

// 切换布局
const changeLayout = (layoutId: number) => {
  selectedLayout.value = layoutId
  console.log('切换布局:', layoutId)
}

// 切换侧边栏
const toggleSidebar = () => {
  isSidebarCollapsed.value = !isSidebarCollapsed.value
}

// 切换全屏模式
const toggleFullscreen = () => {
  if (!document.fullscreenElement) {
    document.documentElement.requestFullscreen().then(() => {
      isFullscreen.value = true
    }).catch(err => {
      console.error(`全屏请求失败: ${err.message}`)
    })
  } else {
    if (document.exitFullscreen) {
      document.exitFullscreen().then(() => {
        isFullscreen.value = false
      }).catch(err => {
        console.error(`退出全屏失败: ${err.message}`)
      })
    }
  }
}

// 过滤树节点
const filterTree = (nodes: TreeNode[], keyword: string): TreeNode[] => {
  if (!keyword) return nodes
  
  return nodes.filter(node => {
    const matchesKeyword = node.name.toLowerCase().includes(keyword.toLowerCase())
    
    if (node.children && node.children.length > 0) {
      const filteredChildren = filterTree(node.children, keyword)
      node.children = filteredChildren
      return filteredChildren.length > 0 || matchesKeyword
    }
    
    return matchesKeyword
  })
}

// 计算过滤后的树数据
const filteredTreeData = computed(() => {
  if (!searchKeyword.value) return treeData
  return filterTree(JSON.parse(JSON.stringify(treeData)), searchKeyword.value)
})

// 获取摄像头状态样式
const getCameraStatusClass = (status?: string) => {
  if (!status) return ''
  return `status-${status}`
}

// 获取摄像头状态文本
const getCameraStatusText = (status?: string) => {
  if (!status) return '未知'
  const statusMap: Record<string, string> = {
    'online': '在线',
    'offline': '离线',
    'warning': '异常'
  }
  return statusMap[status] || '未知'
}

// 注意：移除了使用JSX语法的renderTreeNode函数
// Vue默认使用模板语法，我们已经在template部分实现了树形节点的渲染

// 模拟视频流数据
const videoStreams = ref([])

// 当前活跃的视频单元格
const activeVideoCell = ref(0)

// 设置活跃视频单元格
const setActiveVideoCell = (index: number) => {
  activeVideoCell.value = index
}

// 获取当前选中的摄像头信息
const selectedCameraInfo = computed(() => {
  if (!selectedCamera.value) return null
  
  // 递归查找选中的摄像头
  const findCamera = (nodes: TreeNode[]): TreeNode | null => {
    for (const node of nodes) {
      if (node.id === selectedCamera.value) return node
      if (node.children) {
        const found = findCamera(node.children)
        if (found) return found
      }
    }
    return null
  }
  
  return findCamera(treeData)
})

// 在实际应用中，这里会初始化视频播放组件
onMounted(() => {
  console.log('视频组件已挂载')
  // 初始化视频播放器或连接视频流
})

onUnmounted(() => {
  console.log('视频组件已卸载')
  // 清理视频资源，关闭连接等
})
</script>

<template>
  <div class="video-container" :class="{ 'dark-theme': true }">
    <!-- 顶部导航栏 -->
    <div class="video-header">
      <div class="header-left">
        <div class="logo">
          <img src="/vite.svg" alt="Logo" />
          <span>广东省教育考试保密室实时监控检测系统</span>
        </div>
        <div class="nav-links">
          <router-link to="/" class="nav-link">首页</router-link>
          <router-link to="/video" class="nav-link active">实时视频</router-link>
          <router-link to="/alarm" class="nav-link">告警管理</router-link>
          <router-link to="/stats" class="nav-link">统计分析</router-link>
        </div>
      </div>
      <div class="header-right">
        <div class="system-time">{{ currentTime }}</div>
        <button class="header-btn" title="设置">
          <i class="settings-icon"></i>
        </button>
        <button class="header-btn" title="全屏" @click="toggleFullscreen">
          <i class="fullscreen-icon" :class="{ 'is-fullscreen': isFullscreen }"></i>
        </button>
        <div class="user-info">
          <span class="user-name">管理员</span>
          <span class="user-avatar">A</span>
        </div>
      </div>
    </div>
    
    <!-- 主内容区 -->
    <div class="video-content">
      <!-- 左侧树形菜单 -->
      <div class="sidebar" :class="{ 'collapsed': isSidebarCollapsed }">
        <div class="sidebar-header">
          <div class="tab-group">
            <div class="tab active">实时视频</div>
            <div class="tab">收藏夹</div>
          </div>
        </div>
        
        <div class="exam-info">
          <div class="exam-title">普通高等学校专升本招生考试</div>
          <div class="exam-id">21000002</div>
        </div>
        
        <div class="search-box">
          <input 
            type="text" 
            v-model="searchKeyword" 
            placeholder="输入关键字搜索" 
            class="search-input"
          />
          <button class="search-btn">
            <i class="search-icon"></i>
          </button>
        </div>
        
        <div class="tree-container">
          <div v-for="node in filteredTreeData" :key="node.id" class="tree-root">
            <div 
              class="tree-node-content root-node"
              @click="toggleNode(node)"
            >
              <span class="tree-node-arrow" :class="{ 'expanded': node.isExpanded }">
                <i class="arrow-icon"></i>
              </span>
              <span class="tree-node-icon folder-icon"></span>
              <span class="tree-node-label">{{ node.name }}</span>
              <span class="tree-node-count">{{ node.children?.length || 0 }}</span>
            </div>
            
            <div v-if="node.isExpanded && node.children" class="tree-node-children">
              <template v-for="child in node.children" :key="child.id">
                <div class="tree-node">
                  <div 
                    class="tree-node-content"
                    @click="toggleNode(child)"
                  >
                    <span class="tree-node-arrow" :class="{ 'expanded': child.isExpanded }">
                      <i class="arrow-icon"></i>
                    </span>
                    <span class="tree-node-icon folder-icon"></span>
                    <span class="tree-node-label">{{ child.name }}</span>
                    <span class="tree-node-count">{{ child.children?.length || 0 }}</span>
                  </div>
                  
                  <div v-if="child.isExpanded && child.children" class="tree-node-children">
                    <template v-for="subChild in child.children" :key="subChild.id">
                      <div class="tree-node">
                        <div 
                          class="tree-node-content"
                          @click="toggleNode(subChild)"
                        >
                          <span class="tree-node-arrow" :class="{ 'expanded': subChild.isExpanded }">
                            <i class="arrow-icon"></i>
                          </span>
                          <span class="tree-node-icon folder-icon"></span>
                          <span class="tree-node-label">{{ subChild.name }}</span>
                          <span class="tree-node-count">{{ subChild.children?.length || 0 }}</span>
                        </div>
                        
                        <div v-if="subChild.isExpanded && subChild.children" class="tree-node-children">
                          <div 
                            v-for="camera in subChild.children" 
                            :key="camera.id"
                            class="tree-node"
                          >
                            <div 
                              class="tree-node-content camera-node"
                              :class="{ 
                                'selected': selectedCamera === camera.id,
                                [getCameraStatusClass(camera.status)]: true 
                              }"
                              @click="selectCamera(camera)"
                            >
                              <span class="tree-node-icon camera-icon"></span>
                              <span class="tree-node-label">{{ camera.name }}</span>
                              <span class="camera-status" :class="getCameraStatusClass(camera.status)">
                                {{ getCameraStatusText(camera.status) }}
                              </span>
                            </div>
                          </div>
                        </div>
                      </div>
                    </template>
                  </div>
                </div>
              </template>
            </div>
          </div>
        </div>
        
        <div class="sidebar-footer">
          <div class="footer-item">
            <span>预设位置</span>
            <i class="arrow-icon"></i>
          </div>
          <div class="footer-item">
            <span>云台控制</span>
            <i class="arrow-icon"></i>
          </div>
        </div>
      </div>
      
      <!-- 右侧视频区域 -->
      <div class="video-main" :class="{ 'expanded': isSidebarCollapsed }">
        <div class="video-grid" :class="`layout-${selectedLayout}`">
          <!-- 视频网格 -->
          <div 
            v-for="i in selectedLayout" 
            :key="i"
            class="video-cell"
            :class="{ 'active': activeVideoCell === i-1 }"
            @click="setActiveVideoCell(i-1)"
          >
            <div class="video-cell-header">
              <span class="video-cell-title">{{ selectedCameraInfo?.name || `监控点位 ${i}` }}</span>
              <div class="video-cell-controls">
                <button class="video-control-btn" title="截图"><i class="snapshot-icon"></i></button>
                <button class="video-control-btn" title="录制"><i class="record-icon"></i></button>
                <button class="video-control-btn" title="全屏"><i class="expand-icon"></i></button>
              </div>
            </div>
            <div class="video-placeholder">
              <div class="no-signal">无视频信号</div>
            </div>
            <div class="video-cell-footer">
              <span class="video-time">{{ currentTime }}</span>
              <span class="video-status">离线</span>
            </div>
          </div>
        </div>
        
        <!-- 底部布局选择器 -->
        <div class="layout-selector">
          <div 
            v-for="layout in layoutOptions" 
            :key="layout.id"
            class="layout-option"
            :class="{ 'active': selectedLayout === layout.id }"
            @click="changeLayout(layout.id)"
            :title="layout.description"
          >
            <div class="layout-icon">{{ layout.id }}</div>
            <div class="layout-name">{{ layout.name }}</div>
          </div>
        </div>
      </div>
      
      <!-- 快捷控制面板 -->
      <div class="quick-controls">
        <button class="control-btn" title="播放所有">
          <i class="play-all-icon"></i>
        </button>
        <button class="control-btn" title="停止所有">
          <i class="stop-all-icon"></i>
        </button>
        <button class="control-btn" title="截图">
          <i class="snapshot-icon"></i>
        </button>
        <button class="control-btn" title="录制">
          <i class="record-icon"></i>
        </button>
      </div>
    </div>
  </div>
</template>

<style scoped>
.video-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  width: 100vw;
  overflow: hidden;
  background-color: #f0f2f5;
  /* 移除最小宽度限制，以支持全屏显示 */
  position: fixed;
  top: 0;
  left: 0;
  font-family: -apple-system, BlinkMacSystemFont, 'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  transition: all 0.3s ease;
}

/* 明亮主题 */
.dark-theme {
  background-color: #f5f7fa;
  color: #333333;
}

/* 顶部导航栏样式 */
.video-header {
  height: 60px;
  background-color: #ffffff;
  color: #333333;
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 0 30px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  z-index: 10;
  border-bottom: 1px solid #e8e8e8;
}

.header-left {
  display: flex;
  align-items: center;
}

.logo {
  display: flex;
  align-items: center;
  margin-right: 30px;
}

.logo img {
  height: 28px;
  margin-right: 12px;
  filter: drop-shadow(0 0 2px rgba(0, 140, 255, 0.5));
}

.logo span {
  font-weight: 500;
  font-size: 16px;
  letter-spacing: 0.5px;
  background: linear-gradient(90deg, #2c8cf4, #0052cc);
  -webkit-background-clip: text;
  -webkit-text-fill-color: transparent;
}

.nav-links {
  display: flex;
}

.nav-link {
  color: #666666;
  padding: 0 18px;
  height: 60px;
  display: flex;
  align-items: center;
  text-decoration: none;
  transition: all 0.3s;
  position: relative;
  font-weight: 500;
}

.nav-link:hover {
  color: #2c8cf4;
}

.nav-link.active {
  color: #2c8cf4;
  background-color: rgba(44, 140, 244, 0.1);
}

.nav-link.active::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 0;
  width: 100%;
  height: 3px;
  background-color: #2c8cf4;
}

.header-right {
  display: flex;
  align-items: center;
}

.system-time {
  margin-right: 20px;
  font-size: 14px;
  color: #666666;
  background-color: #f5f7fa;
  padding: 6px 12px;
  border-radius: 4px;
  letter-spacing: 0.5px;
  border: 1px solid #e8e8e8;
}

.header-btn {
  background: none;
  border: 1px solid #e8e8e8;
  color: #666666;
  margin-right: 15px;
  cursor: pointer;
  width: 36px;
  height: 36px;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}

.header-btn:hover {
  background-color: rgba(44, 140, 244, 0.1);
  color: #2c8cf4;
  border-color: #2c8cf4;
}

.settings-icon::before {
  content: '⚙️';
  font-size: 16px;
}

.fullscreen-icon::before {
  content: '⤢';
  font-size: 18px;
  transition: all 0.3s;
}

.fullscreen-icon.is-fullscreen::before {
  content: '⤓';
}

.user-info {
  display: flex;
  align-items: center;
}

.user-name {
  margin-right: 10px;
  font-size: 14px;
  font-weight: 500;
}

.user-avatar {
  background-color: #2c8cf4;
  color: white;
  width: 36px;
  height: 36px;
  border-radius: 50%;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 14px;
  box-shadow: 0 2px 8px rgba(44, 140, 244, 0.5);
}

/* 主内容区样式 */
.video-content {
  display: flex;
  flex: 1;
  position: relative;
  overflow: hidden;
}

/* 侧边栏样式 */
.sidebar {
  width: 320px;
  background-color: #ffffff;
  border-right: 1px solid #e8e8e8;
  display: flex;
  flex-direction: column;
  transition: width 0.3s;
  overflow: hidden;
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
}

.sidebar.collapsed {
  width: 0;
}

.sidebar-header {
  padding: 12px;
  border-bottom: 1px solid #e8e8e8;
  background-color: #f5f7fa;
}

.tab-group {
  display: flex;
  border-bottom: 1px solid #333;
}

.tab {
  padding: 10px 15px;
  cursor: pointer;
  border-bottom: 2px solid transparent;
  color: #666666;
  font-weight: 500;
  transition: all 0.2s;
}

.tab.active {
  border-bottom: 2px solid #2c8cf4;
  color: #2c8cf4;
  background-color: rgba(44, 140, 244, 0.1);
}

.exam-info {
  padding: 12px;
  border-bottom: 1px solid #e8e8e8;
  background-color: #f0f2f5;
}

.exam-title {
  font-size: 16px;
  font-weight: bold;
  margin-bottom: 5px;
  color: #333333;
}

.exam-id {
  font-size: 14px;
  color: #777;
  background-color: rgba(0, 0, 0, 0.05);
  padding: 2px 6px;
  border-radius: 3px;
  display: inline-block;
}

.search-box {
  padding: 12px;
  display: flex;
  border-bottom: 1px solid #e8e8e8;
  background-color: #f0f2f5;
}

.search-input {
  flex: 1;
  padding: 10px 12px;
  border: 1px solid #d9d9d9;
  border-radius: 4px;
  font-size: 16px;
  background-color: #ffffff;
  color: #333333;
  transition: all 0.2s;
}

.search-input:focus {
  border-color: #2c8cf4;
  outline: none;
  box-shadow: 0 0 0 2px rgba(44, 140, 244, 0.2);
}

.search-btn {
  background-color: #2c8cf4;
  border: none;
  margin-left: 8px;
  cursor: pointer;
  width: 36px;
  border-radius: 4px;
  display: flex;
  align-items: center;
  justify-content: center;
  transition: all 0.2s;
}

.search-btn:hover {
  background-color: #1a7fd1;
}

.search-icon::before {
  content: '🔍';
  font-size: 16px;
  color: white;
}

.tree-container {
  flex: 1;
  overflow-y: auto;
  padding: 10px;
  background-color: #ffffff;
}

.tree-node {
  margin-bottom: 2px;
}

.tree-node-content {
  display: flex;
  align-items: center;
  padding: 10px 12px;
  cursor: pointer;
  border-radius: 4px;
  transition: all 0.2s;
  font-size: 16px;
  color: #333333;
  position: relative;
  margin-bottom: 2px;
}

.tree-node-content:hover {
  background-color: #f5f7fa;
  color: #2c8cf4;
}

.tree-node-content.selected {
  background-color: rgba(44, 140, 244, 0.2);
  color: #2c8cf4;
  border-left: 3px solid #2c8cf4;
}

.tree-node-arrow {
  width: 16px;
  height: 16px;
  display: inline-flex;
  align-items: center;
  justify-content: center;
  margin-right: 5px;
  transition: transform 0.2s;
}

.tree-node-arrow .arrow-icon::before {
  content: '▶';
  font-size: 10px;
  color: #777;
}

.tree-node-arrow.expanded .arrow-icon::before {
  content: '▼';
  color: #aaa;
}

.tree-node-icon {
  width: 16px;
  height: 16px;
  margin-right: 5px;
  display: inline-block;
}

.folder-icon::before {
  content: '📁';
  font-size: 14px;
  color: #ffc107;
}

.camera-icon::before {
  content: '📹';
  font-size: 14px;
  color: #2c8cf4;
}

.tree-node-children {
  padding-left: 20px;
  position: relative;
}

.tree-node-children::before {
  content: '';
  position: absolute;
  left: 8px;
  top: 0;
  bottom: 0;
  width: 1px;
  background-color: #444;
}

.tree-node-count {
  margin-left: auto;
  background-color: rgba(0, 0, 0, 0.05);
  padding: 2px 6px;
  border-radius: 10px;
  font-size: 13px;
  color: #666;
}

.camera-status {
  margin-left: auto;
  padding: 2px 6px;
  border-radius: 10px;
  font-size: 13px;
  font-weight: 500;
}

.status-online {
  color: #52c41a;
  background-color: rgba(82, 196, 26, 0.1);
}

.status-offline {
  color: #ff4d4f;
  background-color: rgba(255, 77, 79, 0.1);
}

.status-warning {
  color: #faad14;
  background-color: rgba(250, 173, 20, 0.1);
}

.sidebar-footer {
  border-top: 1px solid #e8e8e8;
  padding: 12px;
  background-color: #f5f7fa;
}

.footer-item {
  display: flex;
  justify-content: space-between;
  padding: 10px 12px;
  cursor: pointer;
  border-radius: 4px;
  margin-bottom: 8px;
  background-color: #ffffff;
  border: 1px solid #e8e8e8;
  transition: all 0.2s;
}

.footer-item:hover {
  background-color: rgba(44, 140, 244, 0.1);
  color: #2c8cf4;
  border-color: #2c8cf4;
}

.arrow-icon::after {
  content: '>';
  font-size: 14px;
  color: #666;
}

/* 视频区域样式 */
.video-main {
  flex: 1;
  background-color: #f5f7fa;
  display: flex;
  flex-direction: column;
  transition: margin-left 0.3s;
  position: relative;
}

.video-main.expanded {
  margin-left: 0;
}

.video-grid {
  flex: 1;
  display: grid;
  gap: 8px;
  padding: 8px;
  background-color: #f0f2f5;
  max-height: calc(100vh - 120px); /* 调整高度以适应全屏模式 */
  height: 100%;
  overflow: hidden;
}

/* 不同布局的网格配置 */
.layout-1 {
  grid-template-columns: 1fr;
  grid-template-rows: 1fr;
}

.layout-4 {
  grid-template-columns: 1fr 1fr;
  grid-template-rows: 1fr 1fr;
}

.layout-6 {
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr;
}

.layout-7 {
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr;
  grid-template-areas: 
    "a a b"
    "a a c"
    "d e f";
}

.layout-8 {
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr;
}

.layout-9 {
  grid-template-columns: 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr;
}

.layout-10 {
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr;
  grid-template-areas: 
    "a a b c"
    "a a d e"
    "f g h i";
}

.layout-13 {
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr;
  grid-template-areas: 
    "a a b c"
    "a a d e"
    "f g h i"
    "j k l m";
}

.layout-16 {
  grid-template-columns: 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr;
}

.layout-25 {
  grid-template-columns: 1fr 1fr 1fr 1fr 1fr;
  grid-template-rows: 1fr 1fr 1fr 1fr 1fr;
}

.video-cell {
  background-color: #ffffff;
  position: relative;
  overflow: hidden;
  border-radius: 6px;
  box-shadow: 0 4px 12px rgba(0, 0, 0, 0.1);
  display: flex;
  flex-direction: column;
  transition: all 0.3s;
  border: 1px solid #e8e8e8;
}

.video-cell.active {
  border-color: #2c8cf4;
  box-shadow: 0 0 0 2px rgba(44, 140, 244, 0.3);
}

.video-cell-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 12px;
  background-color: #f5f7fa;
  border-bottom: 1px solid #e8e8e8;
  z-index: 1;
}

.video-cell-title {
  font-size: 16px;
  font-weight: 500;
  color: #333333;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.video-cell-controls {
  display: flex;
  gap: 5px;
}

.video-control-btn {
  width: 24px;
  height: 24px;
  border-radius: 4px;
  background-color: rgba(0, 0, 0, 0.05);
  border: none;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: #666666;
  transition: all 0.2s;
}

.video-control-btn:hover {
  background-color: rgba(44, 140, 244, 0.1);
  color: #2c8cf4;
}

.snapshot-icon::before {
  content: '📷';
  font-size: 12px;
}

.record-icon::before {
  content: '⏺';
  font-size: 12px;
  color: #ff4d4f;
}

.expand-icon::before {
  content: '⤢';
  font-size: 12px;
}

.video-placeholder {
  flex: 1;
  display: flex;
  align-items: center;
  justify-content: center;
  color: #999;
  font-size: 14px;
  background: linear-gradient(135deg, #111, #222);
  background-size: cover;
  position: relative;
  overflow: hidden;
  box-shadow: inset 0 0 50px rgba(0, 0, 0, 0.5);
  border-radius: 4px;
}

.no-signal {
  background-color: rgba(255, 255, 255, 0.15);
  backdrop-filter: blur(5px);
  padding: 8px 15px;
  border-radius: 4px;
  font-size: 16px;
  color: #e0e0e0;
  letter-spacing: 1px;
  box-shadow: 0 3px 6px rgba(0, 0, 0, 0.2), 0 0 15px rgba(255, 255, 255, 0.1);
  color: #ffffff;
  font-weight: 500;
  display: flex;
  align-items: center;
  border: 1px solid rgba(255, 255, 255, 0.1);
}

.no-signal::before {
  content: '⚠️';
  margin-right: 8px;
  font-size: 16px;
}

.video-cell-footer {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 8px 12px;
  background-color: #f5f7fa;
  border-top: 1px solid #e8e8e8;
  font-size: 14px;
  color: #666666;
}

/* 布局选择器样式 */
.layout-selector {
  height: 70px;
  background-color: #ffffff;
  display: flex;
  align-items: center;
  padding: 0 20px;
  overflow-x: auto;
  justify-content: center; /* 在PC端居中显示布局选项 */
  border-top: 1px solid #e8e8e8;
}

.layout-option {
  width: 50px;
  height: 50px;
  margin: 0 8px;
  background-color: #f5f7fa;
  border-radius: 6px;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: #666666;
  font-size: 16px;
  transition: all 0.2s;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  border: 1px solid #e8e8e8;
  position: relative;
  overflow: hidden;
}

.layout-option.active {
  background-color: rgba(44, 140, 244, 0.1);
  color: #2c8cf4;
  border-color: #2c8cf4;
}

.layout-icon {
  font-weight: bold;
  font-size: 16px;
  margin-bottom: 4px;
}

.layout-name {
  font-size: 12px;
  opacity: 0.8;
}



/* 快捷控制面板 */
.quick-controls {
  position: absolute;
  right: 20px;
  bottom: 80px;
  display: flex;
  flex-direction: column;
  gap: 10px;
  z-index: 10;
  background-color: #ffffff;
  border-radius: 4px;
  padding: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  border: 1px solid #e8e8e8;
}

.control-btn {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: #f5f7fa;
  border: 1px solid #e8e8e8;
  display: flex;
  align-items: center;
  justify-content: center;
  cursor: pointer;
  color: #666666;
  transition: all 0.2s;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

.control-btn:hover {
  background-color: rgba(44, 140, 244, 0.1);
  border-color: #2c8cf4;
  color: #2c8cf4;
  transform: scale(1.1);
}

.play-all-icon::before {
  content: '▶';
  font-size: 16px;
}

.stop-all-icon::before {
  content: '■';
  font-size: 16px;
}
</style>