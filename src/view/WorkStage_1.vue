<template>

  <div class="room-container">

    <div class="top-bar">
      <div class="logo-area">
        <div class="logo">
          <span>本格视界</span>
          <svg width="40" height="10" viewBox="0 0 40 10" fill="none" xmlns="http://www.w3.org/2000/svg">
            <path d="M0 5 C0 7.76142 2.23858 10 5 10 H35 C37.7614 10 40 7.76142 40 5 V5 C40 2.23858 37.7614 0 35 0 H5 C2.23858 0 0 2.23858 0 5 Z" fill="#1A1A1A"/>
          </svg>
        </div>
      </div>
      <div class="title">BenGe.vision</div>

      <div class="nav-actions">
        <div class="user-avatar" @click="toggleUserMenu" ref="userAvatarRef">
          <div class="avatar-text">{{ username?.charAt(0) }}</div>
          <div class="avatar-ripple" v-if="rippleShow"></div>
          <div class="avatar-pulse" v-if="showUserMenu"></div>
        </div>
      </div>

      <!-- 用户菜单 -->
      <transition name="curtain">
        <div v-if="showUserMenu" class="user-menu-container">
          <div class="user-menu">
            <div class="menu-header">
              <div class="menu-avatar">{{ username?.charAt(0) }}</div>
              <div class="menu-user-info">
                <div class="menu-username">{{ username }}</div>
              </div>
            </div>
            <div class="menu-divider"></div>
            <div class="menu-item" @click="quit">
              <img src='../assets/user-menu-quit.png' alt="退出房间" class="menu-icon-img">
              <span>退出房间</span>
            </div>
          </div>
          <div class="curtain-title">
            <span>BenGe.vision</span>
          </div>
        </div>
      </transition>
    </div>

      <!-- 主内容区 -->
      <div class="main-content">
        <!-- 左侧工作区 -->
        <div class="left-panel">
          <h2 class="stage-title">第一阶段：方向确定</h2>

          <!-- 个人工作区 -->
          <div class="section">
            <h3 class="section-title">个人工作区</h3>

            <!-- 背景设定 -->
            <div class="form-item">
              <label>背景设定</label>
              <el-input
                  v-model="worksheet.background"
                  type="textarea"
                  :rows="5"
                  placeholder="请输入故事发生的背景设定（时间、地点、世界观等）"
              />
            </div>

            <!-- 核心创意 -->
            <div class="form-item">
              <label>核心创意</label>
              <el-input
                  v-model="worksheet.coreIdea"
                  type="textarea"
                  :rows="5"
                  placeholder="请输入故事的核心创意或亮点"
              />
            </div>

            <!-- 主题选择 -->
            <div class="form-item">
              <label>主题选择</label>
              <el-select
                  v-model="worksheet.theme"
                  multiple
                  placeholder="请选择主题标签"
                  style="width: 100%; margin-bottom: 10px"
              >
                <el-option
                    v-for="item in themeOptions"
                    :key="item.value"
                    :label="item.label"
                    :value="item.value"
                />
              </el-select>
              <div style="display: flex; gap: 10px">
                <el-input
                    v-model="newThemeInput"
                    placeholder="输入其他主题"
                    style="flex: 1"
                />
                <el-button @click="addCustomTheme">添加</el-button>
              </div>
            </div>

            <!-- 提交按钮 -->
            <div class="submit-area">
              <el-button type="primary" @click="submitWork">提交</el-button>
            </div>
          </div>
        </div>

        <!-- 右侧进度区 -->
        <div class="right-panel">
          <h3 class="section-title">团队进度</h3>

          <!-- 聊天区域 -->
          <Chat />

        </div>
      </div>
    </div>
</template>

<script setup>
import { ref } from 'vue';
import Chat from '@/components/stages/Chat.vue';
import {useRouter} from "vue-router";

const worksheet = ref({
  background: '',
  coreIdea: '',
  theme: [],
});

const router = useRouter();

// 在 <script setup> 中添加
const newThemeInput = ref('');

const addCustomTheme = () => {
  if (newThemeInput.value && !themeOptions.value.some(t => t.value === newThemeInput.value)) {
    themeOptions.value.push({
      value: newThemeInput.value,
      label: newThemeInput.value
    });
    worksheet.value.theme.push(newThemeInput.value);
    newThemeInput.value = '';
  }
};

// 子组件的控制显示的变量
const showUserMenu = ref(false);  // 用户菜单是否显示

// 用户菜单相关部分
const rippleShow = ref(false);  // 涟漪效果是否显示
const userAvatarRef = ref(null);

const toggleRepple = () => {
  rippleShow.value = !rippleShow.value;
  // 涟漪展示一次
  setTimeout(() => {
    rippleShow.value = false;
  }, 800);
}

//返回房间选择页面
const quit = () => {
  router.push({ name: 'cooperatePro' });
}

const toggleUserMenu = () => {
  showUserMenu.value = !showUserMenu.value;
  toggleRepple();
}

const username = ref('');

const themeOptions = ref([
  { value: '硬核推理', label: '硬核推理' },
  { value: '恐怖', label: '恐怖' },
  { value: '情感', label: '情感' },
  { value: '古风', label: '古风' },
  { value: '历史', label: '历史' },
  { value: '欢乐', label: '欢乐' },
  { value: '机制', label: '机制' },
  { value: '民国', label: '民国' },
  { value: '现代', label: '现代' },
  { value: '科幻', label: '科幻' },
  { value: '立意', label: '立意' }
]);



function submitWork() {
  console.log('提交工作内容:', worksheet.value);
  // 这里添加提交逻辑
}

</script>

<style scoped>

/* 页面容器 */
.room-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background-image: url("../assets/loginback.jpg");
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
  color: #333;
}

/* 顶部栏样式 */
.top-bar {
  display: flex;
  justify-content: space-between;
  align-items: center;
  padding: 15px 25px;
  background-color: rgba(255, 255, 255, 0.9);
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
  z-index: 10;
  border-radius: 0 0 12px 12px;
}
.logo-area {
  display: flex;
  flex-direction: column;
  align-items: center;
}
.logo {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 5px;
}
.title {
  font-size: 24px;
  font-weight: bold;
  font-style: italic;
  letter-spacing: 1px;
}
.nav-actions {
  display: flex;
  align-items: center;
  gap: 15px;
}
.main-content {
  display: flex;
  height: calc(100vh - 70px);
  padding: 20px;
  gap: 20px;
}
.user-avatar {
  position: relative;
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: #1a1a1a;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: bold;
  cursor: pointer;
  transition: transform 0.2, box-shadow 0.3;
  /* 11 是为了后续可以通过点击用户来关闭面板 */
  z-index: 11;
  /* hidden 是为了后续的涟漪效果只会在圆圈内部显示 */
  overflow: hidden;
}
.user-avatar:hover {
  transform: scale(1.05);
  box-shadow: 0 0 10px rgba(179, 178, 199, 0.8);
}
.avatar-text {
  font-size: 16px;
  text-transform: uppercase;
  z-index: 2;
}
.avatar-ripple {
  position: absolute;
  /* 设置在父元素内居中 */
  left: 50%;
  top: 50%;
  transform: translate(-50%, -50%);
  /* 刚开始是看不到的 */
  width: 0;
  height: 0;
  border-radius: 50%;
  background-color: rgba(179, 178, 199, 0.2);
  z-index: 1;
  /* 设置无限循环是为了可以在任何时刻显示涟漪效果 */
  animation: ripple 2 infinite;
}
.avatar-pulse{
  position: absolute;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background: rgba(179, 178, 199, 0.2);
  border-radius: 50%;
  z-index: 1;
  animation: pulse-animation 2 infinite;
}
@keyframes ripple {
  0% {
    width: 0;
    height: 0;
    opacity: 0.8;
  }
  100% {
    width: 160%;
    height: 160%;
    opacity: 0;
  }
}
@keyframes pulse-animation {
  0% {
    box-shadow: 0 0 0 0 rgba(179, 178, 199, 0.7);
  }
  70% {
    box-shadow: 0 0 0 10px rgba(179, 178, 199, 0);
  }
  100% {
    box-shadow: 0 0 0 0 rgba(179, 178, 199, 0);
  }
}
.user-menu-container{
  position: fixed;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  backdrop-filter: blur(2px);
  display: flex;
  justify-content: flex-end;
  z-index: 10;
}
.user-menu {
  position: absolute;
  top: 60px;
  right: 25px;
  width: 200px;
  background-color: rgba(255, 255, 255, 0.95);
  border-radius: 10px;
  box-shadow: 0 5px 20px rgba(0, 0, 0, 0.2);
  overflow: hidden;
  z-index: 100;
  color: #333;
  transform-origin: top right;
}
.menu-header {
  display: flex;
  align-items: center;
  gap: 20px;
  padding: 15px;
  background-color: rgba(26, 26, 26, 0.05);
}
.menu-avatar {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: #1a1a1a;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  font-weight: bold;
  margin-right: 12px;
  font-size: 16px;
}

.menu-user-info {
  flex: 1;
}

.menu-username {
  font-weight: 500;
  font-size: 15px;
}

.menu-divider {
  height: 1px;
  background-color: rgba(0, 0, 0, 0.1);
  margin: 0;
}
.menu-item {
  display: flex;
  align-items: center;
  padding: 12px 15px;
  font-size: 14px;
  transition: all 0.2 ease;
  cursor: pointer;
}

.menu-item:hover {
  background-color: rgba(179, 178, 199, 0.3);
}

.menu-icon-img {
  margin-right: 50px;
  width: 20px;
  height: 20px;
  object-fit: contain;
  vertical-align: middle;
}
.curtain-title{
  font-size: 100px;
  font-weight: bold;
  font-style: italic;
  position: absolute;
  letter-spacing: 10px;
  color: #fff;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
.left-panel {
  flex: 3;
  padding: 15px;
}

.right-panel {
  flex: 2;
  padding: 15px;
  display: flex;
  flex-direction: column;
}

.stage-title {
  color: #000000;
  border-bottom: 1px solid #eee;
  padding-bottom: 10px;
  margin-bottom: 20px;
}

.section {
  background-color: #f9f9f9;
  padding: 20px;
  border-radius: 8px;
  margin-bottom: 20px;
}

.section-title {
  color: #050000;
  margin-top: 0;
  margin-bottom: 15px;
  font-size: 16px;
}

.form-item {
  margin-bottom: 20px;
}

.form-item label {
  display: block;
  margin-bottom: 8px;
  font-weight: 500;
  color: #666;
}

.submit-area {
  display: flex;
  justify-content: flex-end;
  margin-top: 30px;
}

</style>
