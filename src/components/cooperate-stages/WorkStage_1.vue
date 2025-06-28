<template>

  <div class="room-container">

      <!-- 主内容区 -->
      <div class="main-content">
        <!-- 左侧工作区 -->
        <div class="left-panel">
          <h2 class="stage-title">第一阶段：方向确定</h2>

          <!-- 个人工作区 -->
          <div class="section" style="flex: 1; display: flex; flex-direction: column;min-height: 0">
            <h3 class="section-title">个人工作区</h3>

            <!--方向选择卡片区域-->
            <div  class="direction-stage-container" style="flex:1; min-height: 0; overflow: hidden">
              <DirectionSelectionStage :userInfo = "username" @submission="handleSubmission"/>
            </div>
          </div>
        </div>

        <!-- 右侧进度区 -->
        <div class="right-panel">

          <!-- 成员区 -->
          <div class="member-area" :class="{ collapsed: !isMemberOpen }">
            <div class="member-header">
              <h2 style="min-width: 72px">成员区</h2>
              <button class="toggle-btn" @click="isMemberOpen = !isMemberOpen">
                {{ isMemberOpen ? '↑' : '↓' }}
              </button>
            </div>
            <transition name="collapse">
              <div v-if="isMemberOpen" class="member-list">
                <div
                    class="member-item"
                    v-for="member in members"
                    :key="member.id"
                >
                  <img :src="member.avatar" alt="avatar" class="member-avatar" />
                  <div style="display: flex; align-items: flex-start;height: 100%;margin-left: 10px;">
                    <span class="member-name">{{ member.name }}</span>
                  </div>
                </div>
              </div>
            </transition>
          </div>

          <!-- 聊天区域 -->
          <div
              class="chat-area"
              :style="{height: isMemberOpen ? '80%' : '95%' }"
          >
            <div>
              <h2>聊天区</h2>
            </div>
            <div
                style="
                width: 100%;
                height: 100%;
                margin-left: auto;
                margin-right: auto;
                position: relative;
                overflow: hidden;
                "
            >
              <Chat :userId="userId" @membersUpdated="updateMembers" :avatar="userAvatar" />
            </div>
          </div>
        </div>
      </div>
    </div>
</template>

<script setup>
import {onBeforeUnmount, onMounted, ref } from 'vue';
import Chat from './Chat.vue';
import DirectionSelectionStage from "@/components/cooperate-stages/DirectionSelectionStage.vue";
// import socket from "yarn/lib/cli";

const userId = "user_" + Math.floor(Math.random() * 1000);
//const username = ref('');
const randomAvatar = Math.floor(Math.random() * 5 + 1);
const userAvatar = require(`@/assets/avatar/${randomAvatar}.jpg`);
const isMemberOpen = ref(true);
const members = ref([]);
/* const submissionInfo = ref('');
   const handleSubmission = (info) => {
  submissionInfo.value = info;
  // 通过 WebSocket发送到服务器，广播给其他用户
  socket.emit('broadcast-submission', {
  // roomId: currentRoomId,  当前房间ID
  submission: info
});
}; */
let provider;

onMounted(async () => {
  // 设置本地用户信息
  //   provider.awareness.setLocalStateField("user", {
  //     id: userId,
  //     name: userName,
  //     avatar: userAvatar,
  //   });
});
onBeforeUnmount(() => {
  provider?.destroy();
});

function updateMembers(membersList) {
  // 直接更新整个成员列表
  members.value = membersList.map((member) => ({
    id: member.id,
    name: member.username,
    avatar: member.avatar || userAvatar,
  }));
  console.log("成员列表已更新:", members.value);
}
</script>

<style scoped>

/* 页面容器 */
.room-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
  background-image: url("../../assets/loginback.jpg");
  background-size: cover;
  background-repeat: no-repeat;
  background-position: center;
  color: #333;
  overflow: hidden;
}

.main-content {
  display: flex;
  height: calc(100vh - 70px);
  padding: 20px;
  gap: 20px;
  overflow: hidden;
  box-sizing: border-box;
  flex: 1;
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

.left-panel {
  display: flex;
  flex-direction: column;
  height: 100%;
  min-height: 0;
  overflow: hidden;
  box-sizing: border-box;
  flex: 3;
  padding: 15px;
}

.right-panel {
  flex: 2;
  display: flex;
  flex-direction: column;
  height: 100%;
  min-height: 0;
  overflow: hidden;
  gap: 20px;
}
.direction-stage-container {
  flex: 1;
  min-height: 0;
  overflow: hidden;
  position: relative;
  display: flex;
  flex-direction: column;
  background-color: #f9f9f9;
  border-radius: 8px;
  padding: 20px;
}

.chat-area {
  flex: 1;
  min-height: 0;
  display: flex;
  flex-direction: column;
  background-color: white;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.05);
  box-sizing: border-box;
  overflow: hidden;
}

.member-header {
  display: flex;
  height: 20px;
  align-items: center;
}

.toggle-btn {
  height: 30px;
  width: 30px;
  border: none;
  background: transparent;
  color: white;
  cursor: pointer;
}

.member-area {
  padding: 20px;
  display: flex;
  flex-direction: column;
  gap: 20px;
  background: white;
  color: black;
  border-radius: 8px;
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.05);
  box-sizing: border-box;
  height: 32%;
}

.member-list {
  display: flex;
  flex-direction: column;
  margin-top: 10px;
  width: 100%;
  max-width: 100%;
  gap: 24px;
  overflow-y: auto;
}

.member-item {
  display: flex;
  align-items: center;
  justify-content: flex-start;
  margin-bottom: 4px;
}

.member-avatar {
  width: 48px;
  height: 48px;
  border-radius: 50%;
  object-fit: cover;
  background-color: #ccc;
}

.member-name {
  font-size: 12px;
  color: white;
  width: 54px;
  overflow: hidden;
  text-overflow: ellipsis;
  white-space: nowrap;
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

.form-item label {
  display: block;
  margin-bottom: 8px;
  font-weight: 500;
  color: #666;
}

.collapse-enter-active,
.collapse-leave-active {
  transition: max-height 0.3 ease, opacity 0.3 ease;
  overflow: hidden;
}

.collapse-enter-from,
.collapse-leave-to {
  max-height: 0;
  opacity: 0;
}

.collapse-enter-to,
.collapse-leave-from {
  max-height: 500px;
  opacity: 1;
}
</style>
