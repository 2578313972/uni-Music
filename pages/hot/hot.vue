<template>
	<view class="all">
		<view class="pic">
			<image :src="img" />
		</view>
		<view class="content">
			<view @click="play(item,index)" v-for="(item,index) in RG" :key="index">
				<view class="c_left">{{index+1}}</view>
				<view class="c_right">
					<view>
						<view>{{item.name}}</view>
						<view>{{item.ar[0].name}}-{{item.name}}</view>
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
				img:'',
				RG:[],
				songName:[],
				name:[],
				allData:[]
			}
		},
		created() {
			uni.request({
				url: 'http://musicapi.leanapp.cn/top/list?idx=1',
				method: 'GET',
				success: res => {
					this.img = res.data.playlist.coverImgUrl
					this.RG = res.data.playlist.tracks.slice(0,50)
				}
			})
		},
		methods:{
			play(data,index){
				console.log(data.id)
				for (let item of this.RG) {
					this.allData.push(item.id)
					this.songName.push(item.name)
					this.name.push(item.ar[0].name)
				}
				uni.navigateTo({
					url:`../palyList/palyList?id=${data.id}&index=${index}&songName=${this.songName}&name=${this.name}&allData=${this.allData}`
				})
			}
		}
	}
</script>

<style scoped>
	.pic>image{width: 100%;height: 350upx;}
	
	.content>view{display: flex;height: 100upx;}
	.c_left{width: 13%;text-align: center;line-height: 100upx;color: #8b8b8b;}
	.c_right{width: 87%;box-sizing: border-box;display: flex;border-bottom: 1px solid #ebebea;justify-content: space-between;}
	.c_right>view:first-child>view:first-child{margin-top: 9px;color: #323233;}
	.c_right>view:first-child>view:last-child{color: #8b8b8b;font-size: 25upx;}
	.c_right>view:last-child{width: 20%;display: flex;}
	.c_right>view:last-child image{width:60upx;height: 60upx;margin: auto;}
</style>
