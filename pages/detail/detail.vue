<template>
	<view class="detail">
		<view class="title">{{detail.title}}</view>
		<view class="info">
			<view class="author">编辑：{{detail.author}}</view>
			<view class="time">发布日期：{{detail.posttime}}</view>
		</view>
		<view class="content">
			<rich-text :nodes="detail.content"></rich-text>
		</view>
	</view>
</template>

<script>
	import {parseTime} from "@/utils/tool.js"
	export default {
		data() {
			return {
				options:null, // 详情id
				detail:{} // 详情内容
			};
		},
		onLoad(e){	// 接收路由参数（只执行一次）		
			this.options=e,
			this.getDetail()
		},
		methods:{
			// 请求获取详情内容
			getDetail(){
				uni.request({
					url:"https://ku.qingnian8.com/dataApi/news/detail.php",
					data:this.options,
					success:res=>{						
						res.data.content=res.data.content.replace(/<img/gi,'<img style="max-width:100%;display: block"') // 修改图片样式						
						res.data.posttime=parseTime(res.data.posttime) // 时间戳修改为时间格式						
						this.detail=res.data
						this.saveHistory() // 本地存储浏览记录
						uni.setNavigationBarTitle({ // 设置详情页标题栏标题
							title:this.detail.title
						})
					}
				})
			},
			// 本地存储浏览记录
			saveHistory(){							
				let historyArr=uni.getStorageSync("historyArr") || []
				let item={
					id:this.detail.id,
					classid:this.detail.classid,
					picurl:this.detail.picurl,
					title:this.detail.title,
					looktime:parseTime(Date.now())
				}
				
				let index=historyArr.findIndex(i=>{ // 循环查找符合return条件的项，找到后返回索引，找不到返回-1
					return i.id==this.detail.id // 通过id判定
				})
				
				if(index>=0){ // 若索引>=0，则表示有相同项，应删除
					historyArr.splice(index,1)
				}
								
				historyArr.unshift(item)	
				historyArr=historyArr.slice(0,10)	// 只显示前10条记录	
				uni.setStorageSync("historyArr",historyArr)
			}
		}
	}
</script>

<style lang="scss">
.detail{
	padding:30rpx;
	.title{
		font-size: 46rpx;
		color:#333;
	}
	.info{
		background: #F6F6F6;
		padding:20rpx;
		font-size: 25rpx;
		color:#666;
		display: flex;
		justify-content: space-between;
		margin:40rpx 0;
	}
	.content{
		padding-bottom:50rpx;
		text-align: justify;
		text-indent: 2em;
		// 小程序不支持穿透改图片样式，须使用上面.replace()动态修改
		// img{
		// 	display: block;
		// 	width: 100%;
		// }
	}
}
</style>
