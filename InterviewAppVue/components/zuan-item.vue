<template>
	<view class="container-zuan-item"  :style="{left:left+'rpx',top:top+'rpx'}" :animation="animationData" @click="addZuanNumber">
		<!-- #ifdef MP-WEIXIN -->
		<image src="../static/images/icon_xz.png" ></image>
		<!-- #endif -->
		<!-- #ifndef MP-WEIXIN -->
		<image src="../../static/images/icon_xz.png" ></image>
		<!-- #endif -->
		<text>{{diamond}}</text>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			};
		},
		props: {
			left: {
				type: Number,
				default: 0
			},
			top: {
				type: Number,
				default: 0
			},
			diamond:{
				type: Number,
				default: 0
			},
			animationData:{
				type:Object,
				default:function () {
					return '';
				}
			}
		},
		methods:{
			addZuanNumber(){
				this.$emit('clickCurZuan');
			}
		}
	}
</script>

<style scoped>
.container-zuan-item{
	position: absolute;
	width: auto;
	height: auto;
	padding: 10rpx 8rpx;
	display: flex;
	flex-direction: column;
	align-items: center;
	justify-content: center;
}
.container-zuan-item image{
	width: 38rpx;
	height: 40rpx;
	border-radius: 50%;
	padding: 16rpx;
	box-shadow:  0rpx 0rpx 15rpx #007AFF;
}
.container-zuan-item text{
	width: auto;
	height: auto;
	font-size: 23rpx;
	color: #FFFFFF;
}
</style>
