<template>
	<view class="container" :style="{ height: pageHeight + 'px', width: pageWidth + 'px' }">
		<!-- v-if="userInfo!=null" -->
		<view class="container-gb" :style="{ height: pageHeight + 'px', width: pageWidth + 'px' }">
			<!-- 背景图 -->
			<image class="container-gb-image" :style="{ height: pageHeight * 1.4 + 'rpx' }" src="../../static/images/bg6_1551427762114.png"></image>
			<!-- 汤钻随机分布区域 -->
			<view class="container-planel-zuan" :style="{ height: pageHeight * 0.9 + 'rpx', width: pageWidth + 'px' }">
				<view class="planel-zuan-content">
					<block v-for="(item, index) in diamondObjList" :key="index">
						<zuan-item v-if="item!=null"
							:top="item.top"
							:left="item.left"
							:diamond="item.diamond"
							:animationData="selectIndex == index ? animationData : ''"
							@clickCurZuan="addZuanNum(index, item.top, item.left)"
						></zuan-item>
					</block>
				</view>
			</view>
		</view>
		<!-- 首页主体内容 -->
		<view class="content" :style="{ height: pageHeight + 'px', width: pageWidth + 'px' }">
			<view class="content-main" :style="{ height: pageHeight + 'px', width: pageWidth + 'px' }">
				<view class="status-bar" :style="{ height: statusBarHeight + 5 + 'px' }"></view>
				<view class="content-nav">
					<view class="nav-one">
						<image src="../../static/images/icon_xz.png"></image>
						<text>汤钻</text>
						<text>{{ userInfo.diamond || 0 }}</text>
					</view>
					<view class="nav-two">
						<image src="../../static/images/icon-jkl.png"></image>
						<text>健康力</text>
						<text>{{ userInfo.power || 0 }}</text>
					</view>
				</view>
				<view class="content-broadcast">
					<view class="broadcast-main">
						<view class="broadcast-main-left"><text>钱包正式上线，活动火热进行中！</text></view>
						<view class="broadcast-main-right">
							<text>更多</text>
							<text>></text>
						</view>
					</view>
				</view>
				<view class="content-equity" :style="{ top: statusBarHeight * 2 + 160 + 'rpx' }">
					<image src="../../static/images/icon_right.png"></image>
					<text>汤钻权益</text>
				</view>
				<view class="content-bottom">
					<view class="content-bottom-item">
						<text>查看区块</text>
						<image src="../../static/images/qkgdjzc.png"></image>
						<text>区块高度及资产</text>
					</view>
					<view class="content-bottom-item">
						<text>使用帮助</text>
						<image src="../../static/images/is_collect.png"></image>
						<text>添加收藏领汤钻</text>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
import zuanItem from '@/components/zuan-item.vue';
export default {
	components: {
		zuanItem
	},
	data() {
		return {
			pageHeight: 0, //页面可用高度
			pageWidth: 0, //页面可用宽度
			statusBarHeight: 0, //页面状态栏的高度
			diamondObjList: [], //汤钻数据列表
			topAndLeftList: [], //每个汤钻的位置（x,y）
			userInfo: null, //用户信息
			selectIndex: -1, //当前被点击汤钻的位置
			animationData: '' //消失动画
		};
	},
	onLoad() {
		let that = this;
		let sysInfo = uni.getSystemInfoSync();
		let pageHeight = sysInfo.windowHeight;
		let pageWidth = sysInfo.windowWidth;
		let statusBarHeight = sysInfo.statusBarHeight;
		that.pageHeight = pageHeight;
		that.pageWidth = pageWidth;
		that.statusBarHeight = statusBarHeight;
		//获取用户信息
		this.getuserInfo();
		
	},
	methods: {
		/**
		 *  获取用户信息
		 */
		getuserInfo() {
			let that = this;
			uni.showLoading({
				title: '正在加载...'
			});
			setTimeout(function() {
				let resultData = uni.getStorageSync('ResultData') || null;
				console.log(resultData);
				//判断缓存中是否有用户信息
				if (resultData == null) {
					//缓存中没有用户信息，需要从服务器中获取数据
					//模拟的数据
					let resultDataJson = `{
						"errorCode": "00",
						"errorMessage": "请求成功",
						"returnObject": {
						  "amount": 0.685,
						  "userInfo": {
							"id": "e94301fbc62343c8b90ac36591a60dac",
							"nickName": "用户昵称",
							"diamond": 12.2345,
							"power": 2000
						  }
						}
					}`;
					//解析JSON数据
					resultData = JSON.parse(resultDataJson).returnObject;
					//把从服务器获取到的数据缓存到本地
					uni.setStorageSync('ResultData', resultData);
				}
				//获取汤钻数据
				if (resultData != null) {
					//把更新数据
					that.userInfo = resultData.userInfo;
					//获取汤钻的随机坐标
					that.simulateZuanlistData(resultData.amount);
				}
			}, 100);
		},

		/**
		 * @param {Object} amount 用户可用的总汤钻数
		 * 模拟10个汤钻坐标数据
		 */
		simulateZuanlistData(amount) {
			console.log('amount:' + amount);
			let that = this;
			let topAndLeftList = [];
			let diamondObjList = [];
			//判断缓存中是否有汤钻坐标信息
			if (uni.getStorageSync('TopAndLeftList') != '' && uni.getStorageSync('DiamondObjList') != '') {
				//缓存中有汤钻坐标信息，则获取并更新
				console.log(uni.getStorageSync('TopAndLeftList'));
				console.log(uni.getStorageSync('DiamondObjList'));
				that.topAndLeftList = uni.getStorageSync('TopAndLeftList');
				that.diamondObjList = uni.getStorageSync('DiamondObjList');
				uni.hideLoading();
				return;
			}
			setTimeout(function() {
				//指定汤钻产生坐标的区域
				let minCardinalHeightNumber = 5;
				let maxCardinalHeightNumber = parseInt(that.pageHeight * 0.8);
				let minCardinalWidthNumber = 2;
				let maxCardinalWidthNumber = parseInt(that.pageWidth * 1.5);
				//获取汤钻坐标列表
				topAndLeftList = that.getRandowNumberList(minCardinalHeightNumber, maxCardinalHeightNumber, minCardinalWidthNumber, maxCardinalWidthNumber);
				for (let i = 0; i < 10; i++) {
					let zuanObj = {
						left: topAndLeftList[i].left,
						top: topAndLeftList[i].top,
						diamond: parseFloat(amount / 10).toFixed(4)
					};
					diamondObjList.push(zuanObj);
				}
				that.topAndLeftList = topAndLeftList;
				that.diamondObjList = diamondObjList;
				//把生产好的汤钻坐标列表缓存到本地
				uni.setStorageSync('TopAndLeftList', topAndLeftList);
				uni.setStorageSync('DiamondObjList', diamondObjList);
				uni.hideLoading();
			}, 100);
		},

		/**
		 *  获取十个不重复的数据
		 */
		getRandowNumberList(minCardinalHeightNumber, maxCardinalHeightNumber, minCardinalWidthNumber, maxCardinalWidthNumber) {
			let randoms = [];
			while (true) {
				let isExists = false;
				// 获取一个10–100范围的随机坐标数
				let leftRandom = parseInt(maxCardinalWidthNumber * Math.random()); //X轴
				let topRandom = parseInt(minCardinalHeightNumber + maxCardinalHeightNumber * Math.random()); //Y轴
				// 判断当前随机数是否已经存在
				if (topRandom < 130 && leftRandom > maxCardinalWidthNumber - 150 && topRandom > maxCardinalHeightNumber - 50) {
					//如果坐标在该区域内则无效
					isExists = true;
				} else { 
					for (let i = 0; i < randoms.length; i++) {
						//判断新产生的坐标是否与之前的坐标数据有区域重叠
						if (Math.abs(topRandom - randoms[i].top) <= 90 && Math.abs(leftRandom - randoms[i].left) <= 70) {
							//有重叠则无效
							isExists = true;
							break;
						} 
					} 
				}
				// 如果不重叠，则添加进去
				if (!isExists)
					randoms.push({
						left: leftRandom,
						top: topRandom
					});
				// 如果有10位随机数了，就跳出
				if (randoms.length === 10) break;
			}
			return randoms;
		},

		/**
		 *  点击增加汤钻数量，该汤钻并消失（执行消失动画）
		 */
		addZuanNum(index, top, left) {
			let that = this;
			let diamondObjList = that.diamondObjList.slice(0); //汤钻数据列表
			let topAndLeftList = that.topAndLeftList.slice(0); //每个汤钻的位置（x,y）
			//创建汤钻消失的动画
			let animation = uni.createAnimation({
				duration: 1000,
				timingFunction: 'ease'
			});
			animation
				.scale(0, 0)
				.step();
			let animationData = animation.export();
			that.animationData = animationData;
			that.selectIndex = index;
			let userInfo = Object.assign({}, that.userInfo);
			let resultData = uni.getStorageSync('ResultData');
			let diamond = parseFloat(userInfo.diamond);
			diamond = (diamond + parseFloat(diamondObjList[index].diamond)).toFixed(4);
			userInfo.diamond = diamond;
			that.userInfo = userInfo;
			//动画执行结束后更新数据
			setTimeout(function() { 
				delete diamondObjList[index];
				delete topAndLeftList[index];
				resultData.userInfo = userInfo;
				//点击后需要把缓存中的数据更新
				uni.setStorageSync('ResultData', resultData);
				uni.setStorageSync('TopAndLeftList', topAndLeftList);
				uni.setStorageSync('DiamondObjList', diamondObjList);
				that.topAndLeftList = topAndLeftList;
				that.diamondObjList = diamondObjList;
			}, 1000);
		}
	}
};
</script>

<style scoped>
page {
	background-color: rgb(4, 16, 74);
}
.container {
	background-color: #000000;
	display: flex;
	flex: 1;
	position: relative;
}
.container-gb {
	display: flex;
	flex: 1;
	/* z-index: 1; */
	position: relative;
	background-color: rgb(4, 16, 74);
	align-items: center;
	justify-content: center;
}
.container-gb-image {
	width: 750rpx;
	z-index: 2;
}
.container-planel-zuan {
	position: absolute;
	z-index: 99;
}
.planel-zuan-content {
	width: 100%;
	height: 100%;
	position: relative;
}

.content {
	width: auto;
	height: auto;
	position: absolute;
	left: 0rpx;
	top: 0rpx;
	z-index: 9;
	background-color: rgba(0, 0, 0, 0);
}
.content-main {
	/* display: flex;
		flex-direction: column;
		align-items: center;
		justify-content: center; */
	display: flex;
	flex-direction: column;
	position: relative;
}
.status-bar {
	height: var(--status-bar-height);
	width: 750rpx;
	background-color: rgba(0, 0, 0, 0);
}
.content-nav {
	width: 500rpx;
	height: 65rpx;
	background-color: rgba(255, 255, 255, 0.1);
	border-radius: 35rpx;
	margin-left: 20rpx;
	display: flex;
	flex-direction: row;
	align-items: center;
	justify-content: center;
}

.nav-one {
	display: flex;
	flex: 1;
	flex-direction: row;
	align-items: center;
	justify-content: center;
}

.nav-one image {
	width: 28rpx;
	height: 35rpx;
	margin: 0rpx 8rpx;
}
.nav-one text {
	width: auto;
	height: auto;
	font-size: 26rpx;
	color: #ffffff;
	margin: 0rpx 3rpx;
}

.nav-two {
	display: flex;
	flex: 1;
	flex-direction: row;
	align-items: center;
	justify-content: center;
}

.nav-two image {
	width: 35rpx;
	height: 35rpx;
	margin: 0rpx 8rpx;
}
.nav-two text {
	width: auto;
	height: auto;
	font-size: 26rpx;
	color: #ffffff;
	margin: 0rpx 3rpx;
}
.content-broadcast {
	width: 750rpx;
	height: 80rpx;
	margin-top: 20rpx;

	display: flex;
	flex-direction: row;
	align-items: center;
	justify-content: center;
}
.broadcast-main {
	width: 680rpx;
	height: 70rpx;
	display: flex;
	flex-direction: row;
	align-items: center;
	justify-content: space-between;
	color: #ffffff;
	font-size: 28rpx;
	border-radius: 45rpx;
	background-color: rgba(255, 255, 255, 0.2);
}

.broadcast-main-left {
	width: 70%;
	height: 100%;
	display: flex;
	flex-direction: row;
	align-items: center;
	justify-content: flex-start;
	padding-left: 30rpx;
}
.broadcast-main-left text {
	width: auto;
	height: auto;
}
.broadcast-main-right {
	width: 30%;
	height: 100%;
	display: flex;
	flex-direction: row;
	align-items: center;
	justify-content: flex-end;
	padding-right: 15rpx;
}

.content-equity {
	width: 200rpx;
	height: auto;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	color: #ffffff;
	font-size: 30rpx;
	padding: 10rpx 15rpx;
	position: absolute;
	right: 0rpx;
}
.content-equity image {
	width: 176rpx;
	height: 176rpx;
}

.content-bottom {
	width: 750rpx;
	height: 280rpx;
	display: flex;
	flex-direction: row;
	align-items: center;
	justify-content: flex-start;
	position: absolute;
	bottom: 20rpx;
	left: 0rpx;
}

.content-bottom-item {
	width: auto;
	height: 90%;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
	padding: 15rpx 36rpx;
	margin: 0rpx 0rpx 30rpx 30rpx;
	background-color: rgba(6, 14, 76, 1);
	border-radius: 15rpx;
	box-shadow: 2rpx 4rpx 8rpx #000000;
}
.content-bottom-item text:nth-child(1) {
	width: auto;
	height: auto;
	font-size: 30rpx;
	color: #007aff;
}

.content-bottom-item text:nth-child(3) {
	width: auto;
	height: auto;
	font-size: 28rpx;
	color: #ffffff;
}
.content-bottom-item image {
	width: 130rpx;
	height: 130rpx;
	margin: 6rpx 0rpx;
}
</style>
