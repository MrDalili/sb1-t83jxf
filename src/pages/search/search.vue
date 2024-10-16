<template>
  <view class="search-container">
    <view class="camera-button" @tap="captureImage">
      <view class="camera-icon">📷</view>
      <text>Search</text>
    </view>
    <view v-if="capturedImage" class="image-preview">
      <image :src="capturedImage" mode="aspectFit"></image>
      <button @tap="confirmImage">Confirm</button>
      <button @tap="retakeImage">Retake</button>
    </view>
    <view v-if="searchResult" class="search-result">
      <text>{{ searchResult }}</text>
      <button @tap="askMore">Ask More</button>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      capturedImage: '',
      searchResult: '',
    }
  },
  methods: {
    captureImage() {
      uni.chooseImage({
        count: 1,
        sourceType: ['camera'],
        success: (res) => {
          this.capturedImage = res.tempFilePaths[0];
        }
      });
    },
    confirmImage() {
      // Here you would implement the OCR and IELTS question analysis
      // For this example, we'll just simulate the process
      this.searchResult = 'Searching...';
      setTimeout(() => {
        this.searchResult = 'This image contains an IELTS Reading passage about climate change. The question type is multiple choice.';
      }, 2000);
    },
    retakeImage() {
      this.capturedImage = '';
      this.searchResult = '';
    },
    askMore() {
      // Transfer the content to the chat interface
      uni.switchTab({
        url: '/pages/chat/chat'
      });
      // You would need to implement a way to pass the search result to the chat page
    }
  }
}
</script>

<style>
.search-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

.camera-button {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  background-color: #007AFF;
  display: flex;
  flex-direction: column;
  justify-content: center;
  align-items: center;
  margin-bottom: 20px;
}

.camera-icon {
  font-size: 40px;
}

.image-preview {
  width: 100%;
  margin-bottom: 20px;
}

.image-preview image {
  width: 100%;
  height: 200px;
}

.search-result {
  width: 100%;
  padding: 10px;
  background-color: #f0f0f0;
  border-radius: 5px;
}
</style>