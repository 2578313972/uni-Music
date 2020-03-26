<template>
	<view class="main">
		<view class="right">
			<view :class="num==index?'bg':''" @click="cli(index)" v-for="(item,index) in letter" :key="index">
				{{item}}
			</view>
		</view>
		<view class="hot">热门</view>
		<view class="hot_list">
			<view v-for="(item,index) in hot" :key="index">
				<view><image :src="item.picUrl" /></view>
				<view>{{item.name}}</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default{
		data(){
			return{
				letter:["热","A","B","C","D","E","F","G","H","I","J","K","L","M","N","O","P","Q","R","S","T","U","V","W","X","Y","Z"],
				hot:[],
				num:0
			}
		},
		methods:{
			cli(data){
				this.num=data
			}
		},
		created() {
			uni.request({
				url: 'http://musicapi.leanapp.cn/top/artists?limit=10',
				method: 'GET',
				success: res => {
					this.hot = res.data.artists
					console.log(res.data)
				}
			})
			uni.request({
				url: 'http://musicapi.leanapp.cn/playlist/hot',
				method: 'GET',
				success: res => {
					// console.log(res)
				}
			})
		}
	}
</script>
		
<style>
	.hot{color: white;background-color: #d9dadb;padding: 3px 0px;padding-left: 15upx;font-size: 25upx;}
	.hot_list{padding-right: 18px;}
	.hot_list>view{display: flex;padding: 10upx;border-bottom: 1px solid #999999;}
	.hot_list>view>view:first-child{width:20%;}
	.hot_list>view>view:first-child image{width:100upx;height: 100upx;}
	.hot_list>view>view:last-child{width:80%;}
	.hot_list>view>view:last-child{line-height: 100upx;font-size: 27upx;}
	
	
	.right{position: fixed;right: 4px;top:145px;z-index: 2;font-size: 27upx;text-align: center;}
	.right>view{margin-bottom: 2px;}
	.right .bg{color: #d44439;}
</style>
