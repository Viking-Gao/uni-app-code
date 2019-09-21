<template>
	<view class="me-container" :style="{ height: pageHeight + 'px', width: pageWidth + 'px' }">
		<view class="container-top"></view>
		<view class="content-userinfo">
			<image src="../../static/images/touxiang.png"></image>
			<text>{{ userInfo.nickName }}</text>
		</view>
		<view class="content-main">
			<view class="content-main-item">
				<view class="content-main-item-left"><image src="../../static/images/icon_xz.png"></image></view>
				<view class="content-main-item-right">
					<text>汤钻</text>
					<text>{{ userInfo.diamond || 0 }}</text>
				</view>
			</view>
			<view class="content-main-item">
				<view class="content-main-item-left"><image src="../../static/images/icon-jkl.png"></image></view>
				<view class="content-main-item-right">
					<text>健康力</text>
					<text>{{ userInfo.power || 0 }}</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
export default {
	data() {
		return {
			userInfo: null,
			pageHeight: 0,
			pageWidth: 0
		};
	},
	onLoad() {
		let that = this;
		let sysInfo = uni.getSystemInfoSync();
		let pageHeight = sysInfo.windowHeight;
		let pageWidth = sysInfo.windowWidth;
		that.pageHeight = pageHeight;
		that.pageWidth = pageWidth;
	},
	onShow() {
		this.userInfo = uni.getStorageSync('ResultData').userInfo;
	},
	methods: {}
};
</script>

<style scoped>
.me-container {
	background-color: #011046;
	position: relative;
}

.content-userinfo {
	width: 750rpx;
	height: 300rpx;
	background-color: #011046;
	display: flex;
	flex-direction: row;
	align-items: center;
	justify-content: center;
}
.content-userinfo image {
	width: 150rpx;
	height: 150rpx;
	border-radius: 50%;
}

.content-userinfo text {
	width: auto;
	height: auto;
	font-size: 38rpx;
	color: #ffffff;
	margin-left: 20rpx;
}
.content-main {
	width: 750rpx;
	height: auto;
	display: flex;
	flex-direction: column;
	align-items: center;
}

.content-main-item {
	width: 550rpx;
	height: 200rpx;
	background-color: #010248;
	box-shadow: 0rpx 0rpx 10rpx #000000;
	display: flex;
	border-radius: 36rpx;
	flex-direction: row;
	align-items: center;
	margin: 30rpx;
}

.content-main-item-left {
	display: flex;
	width: 200rpx;
	height: 90%;
	align-items: center;
	justify-content: center;
}

.content-main-item-left image {
	width: 100rpx;
	height: 100rpx;
}

.content-main-item-right {
	width: auto;
	height: 80%;
	color: #ffffff;
	font-size: 30rpx;
	display: flex;
	flex-direction: column;
	justify-content: center;
}
.content-main-item-right text:nth-child(1) {
	font-size: 33rpx;
	margin-bottom: 10rpx;
}
</style>
