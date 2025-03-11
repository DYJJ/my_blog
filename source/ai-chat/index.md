---
title: AI对话
date: 2023-01-01
updated: 2023-01-01
comments: false
---

{% raw %}
<div class="ai-chat-container">
  <div class="chat-header">
    <h2>AI智能助手</h2>
    <p>使用本地API与AI模型对话（deepseek-r1:8b）</p>
  </div>
  <div class="chat-messages" id="chat-messages">
    <div class="message assistant">
      <div class="message-content">你好！我是AI助手，有什么我可以帮助你的吗？</div>
    </div>
  </div>
  <div class="chat-input-container">
    <textarea id="chat-input" placeholder="输入你的问题..." rows="3"></textarea>
    <button id="send-button">发送</button>
  </div>
</div>

<script>
document.addEventListener('DOMContentLoaded', function() {
  const chatMessages = document.getElementById('chat-messages');
  const chatInput = document.getElementById('chat-input');
  const sendButton = document.getElementById('send-button');
  
  function addMessage(content, role) {
    const messageDiv = document.createElement('div');
    messageDiv.className = `message ${role}`;
    
    const contentDiv = document.createElement('div');
    contentDiv.className = 'message-content';
    contentDiv.textContent = content;
    
    messageDiv.appendChild(contentDiv);
    chatMessages.appendChild(messageDiv);
    
    chatMessages.scrollTop = chatMessages.scrollHeight;
  }
  
  async function sendMessage() {
    const message = chatInput.value.trim();
    if (!message) return;
    
    addMessage(message, 'user');
    chatInput.value = '';
    
    try {
      const loadingDiv = document.createElement('div');
      loadingDiv.className = 'message assistant loading';
      loadingDiv.innerHTML = '<div class="message-content">思考中...</div>';
      chatMessages.appendChild(loadingDiv);
      
      const response = await fetch('http://localhost:11434/api/generate', {
        method: 'POST',
        headers: {
          'Content-Type': 'application/json'
        },
        body: JSON.stringify({
          model: 'deepseek-r1:8b',
          prompt: message,
          stream: false
        })
      });
      
      chatMessages.removeChild(loadingDiv);
      
      if (!response.ok) {
        throw new Error('API请求失败');
      }
      
      const data = await response.json();
      addMessage(data.response, 'assistant');
    } catch (error) {
      console.error('发送消息失败:', error);
      addMessage('抱歉，发生了错误，请稍后再试。', 'assistant error');
    }
  }
  
  sendButton.addEventListener('click', sendMessage);
  
  chatInput.addEventListener('keydown', function(e) {
    if (e.key === 'Enter' && !e.shiftKey) {
      e.preventDefault();
      sendMessage();
    }
  });
});
</script>

<style>
.ai-chat-container {
  max-width: 800px;
  margin: 0 auto;
  border-radius: 10px;
  overflow: hidden;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.1);
  background-color: var(--grey-0);
}

.chat-header {
  padding: 15px;
  background-color: var(--color-primary);
  color: #fff;
  text-align: center;
}

.chat-header h2 {
  margin: 0;
  font-size: 1.5rem;
}

.chat-header p {
  margin: 5px 0 0;
  font-size: 0.9rem;
  opacity: 0.8;
}

.chat-messages {
  height: 400px;
  overflow-y: auto;
  padding: 15px;
  display: flex;
  flex-direction: column;
  gap: 15px;
}

.message {
  max-width: 80%;
  padding: 10px 15px;
  border-radius: 18px;
  line-height: 1.5;
}

.message.user {
  align-self: flex-end;
  background-color: var(--color-primary);
  color: #fff;
  border-bottom-right-radius: 5px;
}

.message.assistant {
  align-self: flex-start;
  background-color: var(--grey-1);
  color: var(--text-color);
  border-bottom-left-radius: 5px;
}

.message.assistant.loading {
  background-color: var(--grey-2);
  color: var(--grey-5);
}

.message.assistant.error {
  background-color: #ffdddd;
  color: #d32f2f;
}

.chat-input-container {
  display: flex;
  padding: 15px;
  border-top: 1px solid var(--grey-3);
  background-color: var(--grey-1);
}

#chat-input {
  flex: 1;
  padding: 12px;
  border: 1px solid var(--grey-3);
  border-radius: 20px;
  resize: none;
  background-color: var(--grey-0);
  color: var(--text-color);
}

#send-button {
  margin-left: 10px;
  padding: 0 20px;
  background-color: var(--color-primary);
  color: white;
  border: none;
  border-radius: 20px;
  cursor: pointer;
  transition: background-color 0.2s;
}

#send-button:hover {
  background-color: var(--color-primary-dark, #1976d2);
}

@media (max-width: 768px) {
  .message {
    max-width: 90%;
  }
}
</style>
{% endraw %} 