<template>
  <view class="chat-container">
    <scroll-view class="chat-messages" scroll-y="true" :scroll-top="scrollTop">
      <view v-for="(message, index) in messages" :key="index" :class="['message', message.type]">
        <text>{{ message.content }}</text>
      </view>
    </scroll-view>
    <view class="input-area">
      <input v-model="inputMessage" type="text" placeholder="Type your message..." @confirm="sendMessage" />
      <button @tap="sendMessage" :disabled="isLoading">Send</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      messages: [],
      inputMessage: '',
      scrollTop: 0,
      apiKey: 'sk-2c49a5aa51ce4a4693298c8ef2338e27',
      conversationHistory: [],
      isLoading: false
    }
  },
  methods: {
    async sendMessage() {
      if (!this.inputMessage.trim() || this.isLoading) return;

      this.isLoading = true;
      this.addMessage('user', this.inputMessage);
      this.conversationHistory.push({ role: 'user', content: this.inputMessage });
      const userInput = this.inputMessage;
      this.inputMessage = '';

      try {
        const response = await fetch('https://api.deepseek.com/v1/chat/completions', {
          method: 'POST',
          headers: {
            'Authorization': `Bearer ${this.apiKey}`,
            'Content-Type': 'application/json'
          },
          body: JSON.stringify({
            model: 'deepseek-chat',
            messages: this.conversationHistory,
            max_tokens: 1000,
            stream: true
          })
        });

        if (!response.ok) {
          throw new Error(`HTTP error! status: ${response.status}`);
        }

        const reader = response.body.getReader();
        const decoder = new TextDecoder('utf-8');
        let aiResponse = '';
        let aiMessageId = this.addMessage('ai', '');

        while (true) {
          const { done, value } = await reader.read();
          if (done) break;
          
          const chunk = decoder.decode(value);
          const lines = chunk.split('\n');
          
          for (const line of lines) {
            if (line.startsWith('data:')) {
              const jsonData = line.slice(5).trim();
              if (jsonData === '[DONE]') continue;
              
              try {
                const parsedData = JSON.parse(jsonData);
                const content = parsedData.choices[0].delta.content;
                if (content) {
                  aiResponse += content;
                  this.updateMessage(aiMessageId, aiResponse);
                }
              } catch (e) {
                console.error('Error parsing JSON:', e);
              }
            }
          }
        }

        this.conversationHistory.push({ role: 'assistant', content: aiResponse });
      } catch (error) {
        console.error('Error calling DeepSeek API:', error);
        this.addMessage('ai', 'Sorry, I encountered an error. Please try again.');
      } finally {
        this.isLoading = false;
      }
    },
    addMessage(type, content) {
      const message = { type, content };
      this.messages.push(message);
      this.$nextTick(() => {
        this.scrollTop = 9999;
      });
      return this.messages.length - 1; // Return the index of the new message
    },
    updateMessage(index, content) {
      if (index >= 0 && index < this.messages.length) {
        this.messages[index].content = content;
        this.$nextTick(() => {
          this.scrollTop = 9999;
        });
      }
    }
  }
}
</script>

<style>
.chat-container {
  display: flex;
  flex-direction: column;
  height: 100vh;
}

.chat-messages {
  flex: 1;
  padding: 10px;
}

.message {
  margin-bottom: 10px;
  padding: 10px;
  border-radius: 5px;
  max-width: 80%;
}

.user {
  align-self: flex-end;
  background-color: #007AFF;
  color: white;
}

.ai {
  align-self: flex-start;
  background-color: #E5E5EA;
  color: black;
}

.input-area {
  display: flex;
  padding: 10px;
  border-top: 1px solid #ccc;
}

input {
  flex: 1;
  padding: 5px;
  margin-right: 10px;
}

button {
  padding: 5px 10px;
}

button:disabled {
  opacity: 0.5;
}
</style>