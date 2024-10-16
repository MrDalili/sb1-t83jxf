<template>
  <view class="profile-container">
    <view class="avatar">
      <image src="/static/avatar.png" mode="aspectFit"></image>
    </view>
    <view class="info">
      <text class="name">John Doe</text>
      <text class="email">johndoe@example.com</text>
    </view>
    <view class="stats">
      <view class="stat-item">
        <text class="stat-value">10</text>
        <text class="stat-label">Completed Tests</text>
      </view>
      <view class="stat-item">
        <text class="stat-value">7.5</text>
        <text class="stat-label">Average Score</text>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      // You can add reactive data here if needed
    }
  },
  // Add any necessary methods or lifecycle hooks
}
</script>

<style>
.profile-container {
  display: flex;
  flex-direction: column;
  align-items: center;
  padding: 20px;
}

.avatar {
  width: 100px;
  height: 100px;
  border-radius: 50%;
  overflow: hidden;
  margin-bottom: 20px;
}

.avatar image {
  width: 100%;
  height: 100%;
}

.info {
  text-align: center;
  margin-bottom: 20px;
}

.name {
  font-size: 24px;
  font-weight: bold;
}

.email {
  font-size: 16px;
  color: #666;
}

.stats {
  display: flex;
  justify-content: space-around;
  width: 100%;
}

.stat-item {
  text-align: center;
}

.stat-value {
  font-size: 24px;
  font-weight: bold;
}

.stat-label {
  font-size: 14px;
  color: #666;
}
</style>