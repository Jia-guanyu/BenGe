<template>
  <div class="chat-container">
    <!-- 顶部标题栏 -->
    <div class="chat-header">
      <button class="member-toggle" @click="toggleMemberList">
        <i class="toggle-icon">{{ isMemberListVisible ? '×' : '≡' }}</i>
      </button>
      <h2 class="room-name">{{ roomName }}</h2>
    </div>

    <!-- 成员列表侧边栏 -->
    <div class="member-list-overlay" v-show="isMemberListVisible" @click="toggleMemberList"></div>
    <div class="member-list" :class="{ 'member-list-visible': isMemberListVisible }">
      <h3>群成员 ({{ members.length }})</h3>
      <ul>
        <li v-for="member in members" :key="member.id" class="member-item">
          <div class="avatar-placeholder"></div>
          <span>{{ member.name }}</span>
        </li>
      </ul>
    </div>

    <!-- 聊天内容区域 -->
    <div class="chat-messages" ref="messagesContainer">
      <div
          v-for="(message, index) in messages"
          :key="index"
          :class="['message', message.isMe ? 'message-me' : 'message-other']"
      >
        <div class="message-avatar">
          <div class="avatar-circle"></div>
        </div>
        <div class="message-content">
          <div class="message-sender">{{ message.sender }}</div>
          <div class="message-bubble">{{ message.content }}</div>
          <div class="message-time">{{ message.time }}</div>
        </div>
      </div>
    </div>

    <!-- 输入区域 -->
    <div class="chat-input-area">
      <input
          type="text"
          v-model="newMessage"
          @keyup.enter="sendMessage"
          placeholder="输入消息..."
          class="message-input"
      />
      <button @click="sendMessage" class="send-button">发送</button>
    </div>
  </div>
</template>

<script>
export default {
  name: 'ChatComponent',
  props: {
    roomName: {
      type: String,
      default: '群聊房间'
    },
    members: {
      type: Array,
      default: () => []
    },
    initialMessages: {
      type: Array,
      default: () => []
    }
  },
  data() {
    return {
      isMemberListVisible: false,
      newMessage: '',
      messages: this.initialMessages
    }
  },
  methods: {
    toggleMemberList() {
      this.isMemberListVisible = !this.isMemberListVisible;
    },
    sendMessage() {
      if (this.newMessage.trim() === '') return;

      const newMsg = {
        sender: '我',
        content: this.newMessage,
        time: this.getCurrentTime(),
        isMe: true
      };

      this.messages.push(newMsg);
      this.newMessage = '';

      // 滚动到底部
      this.$nextTick(() => {
        this.$refs.messagesContainer.scrollTop = this.$refs.messagesContainer.scrollHeight;
      });
    },
    getCurrentTime() {
      const now = new Date();
      return `${now.getHours()}:${now.getMinutes().toString().padStart(2, '0')}`;
    }
  },
  watch: {
    initialMessages(newVal) {
      this.messages = newVal;
    }
  }
}
</script>

<style scoped>
.chat-container {
  display: flex;
  flex-direction: column;
  height: 100%;
  width: 100%;
  background-color: #f5f5f5;
  border-radius: 8px;
  overflow: hidden;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
}

/* 头部样式 */
.chat-header {
  display: flex;
  align-items: center;
  padding: 12px 16px;
  background-color: #5e53c1;
  color: white;
  position: relative;
}

.member-toggle {
  background: none;
  border: none;
  color: white;
  font-size: 20px;
  cursor: pointer;
  margin-right: 12px;
  padding: 4px 8px;
}

.room-name {
  margin: 0;
  font-size: 18px;
  font-weight: normal;
}

/* 成员列表样式 */
.member-list-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.5);
  z-index: 20;
}

.member-list {
  position: fixed;
  top: 0;
  left: -250px;
  width: 250px;
  height: 100%;
  background-color: white;
  box-shadow: 2px 0 5px rgba(0, 0, 0, 0.1);
  transition: transform 0.3s ease;
  z-index: 30;
  padding: 60px 16px 16px;
  overflow-y: auto;
}

.member-list-visible {
  transform: translateX(250px);
}

.member-item {
  display: flex;
  align-items: center;
  padding: 8px 0;
  border-bottom: 1px solid #eee;
}

/* 聊天消息区域 */
.chat-messages {
  flex: 1;
  padding: 16px;
  overflow-y: auto;
  background-color: #ece3dd;
}

.message {
  display: flex;
  margin-bottom: 16px;
}

.message-me {
  flex-direction: row-reverse;
}

.message-avatar {
  flex-shrink: 0;
  margin: 0 8px;
}

.avatar-circle {
  width: 40px;
  height: 40px;
  border-radius: 50%;
  background-color: #ddd;
  background-size: cover;
  background-position: center;
}

.message-content {
  max-width: 70%;
}

.message-sender {
  font-size: 12px;
  color: #666;
  margin-bottom: 4px;
}

.message-bubble {
  padding: 10px 14px;
  border-radius: 18px;
  line-height: 1.4;
  word-break: break-word;
}

.message-me .message-bubble {
  background-color: #95ec69;
  border-top-right-radius: 4px;
}

.message-other .message-bubble {
  background-color: white;
  border-top-left-radius: 4px;
}

.message-time {
  font-size: 11px;
  color: #999;
  margin-top: 4px;
}

.message-me .message-time {
  text-align: right;
}

/* 输入区域样式 */
.chat-input-area {
  display: flex;
  padding: 12px;
  background-color: #f0f0f0;
  border-top: 1px solid #ddd;
}

.message-input {
  flex: 1;
  padding: 10px 12px;
  border: 1px solid #ddd;
  border-radius: 20px;
  outline: none;
  font-size: 14px;
}

.send-button {
  margin-left: 12px;
  padding: 10px 20px;
  background-color: #5e53c1;
  color: white;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  outline: none;
}

.send-button:hover {
  background-color: #27ae60;
}
</style>
