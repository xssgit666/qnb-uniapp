<template>
	<view class="home">
		<scroll-view class="navscroll" scroll-x="true">
			<view
				class="item"
				:class="index==navIndex ? 'active' : ''"
				v-for="(item,index) in navArr" 
				@click="clickNav(index,item.id)"
			>
				{{item.classname}}
			</view>
		</scroll-view>
		<view class="content">
			<view
				class="row"
				v-for="item in newsArr"
				:key="item.id"
			>
				<newsbox
					:item="item"
					@click.native="goDetail(item)"
				></newsbox>
			</view>
		</view>
		<view class="nodata" v-if="!newsArr.length">
			<image src="../../static/images/nodata.png" mode="widthFix"></image>
		</view>
		<view class="loading" v-if="newsArr.length">			
			<view v-if="loading==1">数据加载中...</view>
			<view v-if="loading==2">没有更多了~~</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				navIndex: 0,
				navArr:[], // 导航栏数组
				newsArr:[], // 新闻列表数组
				currentPage:1,
				loading:0 //0默认  1加载中  2没有更多了
			}
		},
		onLoad() {
			this.getNavData();
			this.getNewsData();
		},
		onReachBottom(){ // 页面滚动到底部的事件
		    if(this.loading==2){
				return;
			}
			this.currentPage++;
			this.loading=1;
			this.getNewsData();
		},
		methods: {
			// 点击导航切换
			clickNav(index,id){
				this.navIndex=index,
				this.currentPage=1, // 切换时加载第一页
				this.newsArr=[], // 把上个导航内容清空
				this.loading=0,
				this.getNewsData(id)
			},
			
			// 跳转到详情页
			goDetail(item){
				uni.navigateTo({
					url:`/pages/detail/detail?cid=${item.classid}&id=${item.id}`
				})
			},
			
			//获取导航列表数据
			getNavData(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/navlist.php",
					success:res=>{
						this.navArr=res.data
					}
				})
			},
			
			//获取新闻列表数据
			getNewsData(id=50){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/newslist.php",
					data:{						
						cid:id,
						page:this.currentPage
					},
					success:res=>{
						if(res.data.length==0){
							this.loading=2
						}
						this.newsArr=[...this.newsArr,...res.data]
					}
				})
			}
		}
	}
</script>

<style lang="less" scoped>
	.navscroll{
		height: 100rpx;
		background: #F7F8FA;
		white-space: nowrap;
		position: fixed;
		top:var(--window-top); // 设置H5端内容区域距离顶部的距离(NavigationBar 的高度)
		left:0;
		z-index: 10;
		
		// 隐藏H5端滚动条
		/deep/ ::-webkit-scrollbar {
			width: 4px !important;
			height: 1px !important;
			overflow: auto !important;
			background: transparent !important;
			-webkit-appearance: auto !important;
			display: block;
		}
		
		.item{
			font-size: 40rpx;
			display: inline-block;
			line-height: 100rpx;
			padding:0 30rpx;
			color:#333;		
			&.active{ // 同级类名加&
				color:#31C27C;
			}
		}
	}
	
	.content{
		padding:100rpx 30rpx 0;
		padding-top:100rpx;	
		.row{
			margin: 30rpx 0;
		}
	}
	.nodata{
		display: flex;
		justify-content: center;
		image{
			width: 360rpx;
		}
	}
	.loading{
		text-align: center;
		font-size: 26rpx;
		color:#888;
		line-height: 2em;
	}
</style>
