<template>
	<view>
		<view v-if="id==0" class="TJ">
			<view class="TJ_first" @click="alllist">推荐音乐<text>></text></view>
			<view class="TJ_B">
				<view class="TJ_box" @click="TJ_list(item)" v-for="item in TJ" :key="item">
					<view class="TJ_box1">
						<image :src="item.picUrl" />
						<view>
							<text></text>{{item.trackCount}}万
						</view>
					</view>
					<view class="TJ_box2">
						{{item.name}}
					</view>
				</view>
			</view>
		</view>
		
		
		<view v-if="id==1" class="PH">
			<view @click="alllist" class="PH_first">排行榜<text>></text></view>
			<view class="PH_B">
				<view class="PH_box" @click="TJ_list(item)" v-for="item in PH" :key="item">
					<view class="PH_box1">
						<image :src="item.coverImgUrl" />
						<view>
							<text></text>{{item.subscribedCount}}万
						</view>
					</view>
					<view class="PH_box2">
						{{item.name}}
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data(){
			return {
				TJ:[],
				PH:[],
				id:""
			}
		},
		onLoad(data) {
			this.id = data.id
			uni.request({
				url: 'http://musicapi.leanapp.cn/personalized',
				method: 'GET',
				success: res => {
					this.TJ = res.data.result
				}
			});
			uni.request({
				url: 'http://musicapi.leanapp.cn/toplist/detail',
				method: 'GET',
				success: res => {
					this.PH = res.data.list
				}
			});
		}
	}
</script>

<style>
	.TJ_first{font-size: 38upx;margin: 30upx 0upx 20upx 20upx;}
	.TJ_first text{font-size: 44upx;}
	
	.TJ_B{display: flex;flex-wrap: wrap;justify-content: space-around;}
	.TJ_box{width:33%;height:350upx;margin-bottom: 30upx;}
	
	.TJ_box1{position: relative;}
	.TJ_box1 image{width:100%;height:250upx;border-radius: 15upx;}
	.TJ_box1 view{position: absolute;top: 10upx;color: white;right: 5px;}
	.TJ_box1 view text{display: inline-block;background: url(../../static/iconall.png)no-repeat 2px -23px;width:20px;height: 12px;}
	
	.TJ_box2{font-size: 24upx;box-sizing: border-box;padding: 0px 10upx;}
	
	
	.PH_first{font-size: 38upx;margin: 30upx 0upx 20upx 20upx;}
	.PH_first text{font-size: 44upx;}
	
	.PH_B{display: flex;flex-wrap: wrap;justify-content: space-around;}
	.PH_box{width:33%;height:350upx;margin-bottom: 30upx;}
	
	.PH_box1{position: relative;}
	.PH_box1 image{width:100%;height:250upx;border-radius: 15upx;}
	.PH_box1 view{position: absolute;top: 10upx;color: white;right: 5px;}
	.PH_box1 view text{display: inline-block;background: url(../../static/iconall.png)no-repeat 2px -23px;width:20px;height: 12px;}
	
	.PH_box2{font-size: 24upx;box-sizing: border-box;padding: 0px 10upx;}
</style>
