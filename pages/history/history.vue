<template>
	<view class="history">
		<view class="content">
			<view class="row" v-for="item in listArr">
				<newsbox :item="item" @click.native="goDetail(item)"></newsbox>
			</view>			
		</view>
		
		<view class="nohistory" v-if="!listArr.length">
			<image src="../../static/images/nohis.png" mode="widthFix"></image>
			<view class="text">暂无浏览记录</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				listArr:[]
			};
		},
		onShow(){ // 一展示便调用
			this.getData()
		},
		methods:{
			// 跳转到详情页
			goDetail(){
				uni.navigateTo({
					url:"/pages/detail/detail"
				})
			},
			//获取缓存浏览记录
			getData(){
				let hisArr=uni.getStorageSync("historyArr") || []
				this.listArr=hisArr
				console.log(this.listArr)
			}
		}
	}
</script>

<style lang="less">
	.content{
		padding:0 30rpx;
		.row{
			margin: 30rpx 0;
		}
	}
	.nohistory{
		display: flex;
		flex-direction: column;
		justify-content: center;
		align-items: center;
		image{
			width: 450rpx;
		}
		.text{
			font-size: 26rpx;
			color:#888;
		}
	}
</style>
