<template>
	<view class="all">
		<view class="header">
			<view class="h_left">
				<view>
					<image src="../../static/play.png" />
				</view>
				<view>播放全部<text>(共{{list.length}}首)</text></view>
			</view>
			<view class="h_right">+收藏({{SC}})</view>
		</view>
		
		
		<view class="content">
			<view @click="playlist(item,index)" v-for="(item,index) in list" :key="index">
				<view class="c_left">{{index+1}}</view>
				<view class="c_right">
					<view>
						<view>{{item.name}}</view>
						<view>{{item.ar.length>1?item.ar[0].name+" / "+item.ar[1].name:item.ar[0].name}} - {{item.name}}</view>
					</view>
					<view>
						<image src="../../static/play.png" />
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default{
		data(){
			return{
				SC:'',
				list:[],
				id:[],
				songName:[],
				name:[]
			}
		},
		methods:{
			playlist(data,index){
				for (let item of this.list) {
					this.id.push(item.id)
					this.songName.push(item.name)
					this.name.push(item.ar[0].name)
				}
				uni.navigateTo({
					url:`../palyList/palyList?id=${data.id}&allData=${this.id}&index=${index}&songName=${this.songName}&name=${this.name}`
				})
			}
		},
		onLoad(data) {
			uni.request({
				url: `http://musicapi.leanapp.cn/playlist/detail?id=${data.id}`,
				method: 'GET',
				success: res => {
					this.SC = res.data.playlist.commentCount
					this.list = res.data.playlist.tracks
				}
			})
		}
	}
</script>

<style scoped>
	
	.header{display: flex;height: 85upx;line-height: 85upx;}
	.h_left{display: flex;width:60%;border-bottom: 1px solid #EEEEEE;}
	.h_left>view:first-child{display: flex;justify-content: space-around;padding-left: 40upx;}
	.h_left>view:last-child>text{color: #999;}
	.h_left>view image{width:50upx;height:50upx;margin: auto;}
	.h_right{width:40%;background-color: #ff0000;color: white;text-align: center;}
	
	.content>view{display: flex;height: 100upx;}
	.c_left{width: 13%;text-align: center;line-height: 100upx;color: #8b8b8b;}
	.c_right{width: 87%;box-sizing: border-box;display: flex;border-bottom: 1px solid #ebebea;justify-content: space-between;}
	.c_right>view:first-child>view:first-child{margin-top: 9px;color: #323233;}
	.c_right>view:first-child>view:last-child{color: #8b8b8b;font-size: 25upx;}
	.c_right>view:last-child{width: 20%;display: flex;}
	.c_right>view:last-child image{width:60upx;height: 60upx;margin: auto;}
</style>
