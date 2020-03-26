<template>
	<view class="all">
		<view class="top">
			<view @click="back"><image src="../../static/arrow_left.png" /></view>
			<view>{{songName}}</view>
		</view>
		
		<view @click="listShow=false" :style="{height:height+'px'}" class="content">
			<view :style="{height:height+'px'}" class="con_one"><image :style="{height:height+'px'}" :src="picUrl" /></view>
			<view class="one1">{{songName}}</view>
			<view class="one2">{{name}}</view>
			<view class="ball" :style="{transform:'rotate('+deg+'deg)'}" >
				<image  :src="picUrl" />
			</view>
			
			<view class="slider">
				<text>{{"0"+musicUseTimeMinutes}}:{{useSecond}}</text><slider @change="chang" block-size="12" :value="musicUseTime" min="0" :max="musicTime"  /><text>{{"0"+musicTimeMinutes}}:{{second}}</text>
			</view>
			
			<view class="footer">
				<view @click.stop="mode==2?mode-=2:mode+=1" class="f_one box"><image :src="modeImg[mode]" /></view>
				<view @click.stop="backPage" class="f_two box"><image src="../../static/top.png" /></view>
				<view @click.stop="stop" class="f_three box"><image :src="play?img1:img2" /></view>
				<view @click.stop="next" class="f_fore box"><image src="../../static/but.png" /></view>
				<view @click.stop="listShow=!listShow" class="f_five box"></view>
			</view>
			<!-- 所有歌曲 -->
			<view v-if="listShow" class="list">
				<view>
					<view @click.stop="tabSong(index)" :class="allIndex == index?'bgcolor':''" v-for="(item,index) in listSongName" :key="index">
						<view class="list_one">
							<view>{{item}}</view>
							<view>{{listName[index]}}</view>
						</view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data(){
			return{
				height:0, //-------------------------------------------------------------------------------------------- 屏幕高
				picUrl:"", //------------------------------------------------------------------------------------------- 图片路径
				songName:'', //----------------------------------------------------------------------------------------- 歌名
				name:'', //--------------------------------------------------------------------------------------------- 歌手
				img1:'../../static/zt-1.png', //------------------------------------------------------------------------ 播放图片
				img2:'../../static/bf.png', //-------------------------------------------------------------------------- 暂停图片
				modeImg:["../../static/list.png","../../static/danqu.png","../../static/suiji.png"], //----------------- 列表循环、单曲循环、随机播放图片
				mode:0, //---------------------------------------------------------------------------------------------- 列表循环、单曲循环、随机播放图片的下标
				play:true, //------------------------------------------------------------------------------------------- 控制播放暂停
				allData:[], //------------------------------------------------------------------------------------------ 列表id数组
				listName:[], //----------------------------------------------------------------------------------------- 列表歌手数组
				listSongName:[], //------------------------------------------------------------------------------------- 列表歌名数组
				allIndex:0, //------------------------------------------------------------------------------------------ 当前歌曲下标
				listShow:false, //-------------------------------------------------------------------------------------- 所有列表显示隐藏
				deg:0, //----------------------------------------------------------------------------------------------- 旋转度数
				musicTime:0, //----------------------------------------------------------------------------------------- 音乐时长
				musicTimeMinutes:0, //---------------------------------------------------------------------------------- 音乐时长分钟
				musicTimeSecond:0, //----------------------------------------------------------------------------------- 音乐时长秒
				musicUseTime:0 ,//-------------------------------------------------------------------------------------- 使用时间
				musicUseTimeMinutes:0 ,//------------------------------------------------------------------------------- 使用时间(分钟)
				musicUseTimeSecond:0 //--------------------------------------------------------------------------------- 使用时间(秒)
			}
		},
		computed:{
			useSecond(){
				if(this.musicUseTimeSecond<10){
					return "0"+this.musicUseTimeSecond
				}
				return this.musicUseTimeSecond
			},
			second(){
				if(this.musicTimeSecond<10){
					return "0"+this.musicTimeSecond
				}
				return this.musicTimeSecond
			}
		},
		methods:{
			back(){ // 返回
				uni.navigateBack()
			},
			backPage(){ // 上一页
				if(this.allIndex==0){
					this.allIndex = this.allData.length-1
				}else{
					this.allIndex = parseFloat(this.allIndex)-1
				}
				uni.request({
					url:`http://musicapi.leanapp.cn/song/detail?ids=${this.allData[this.allIndex]}`,
					method: 'GET',
					success: res => {
						this.picUrl = res.data.songs[0].al.picUrl
						this.songName = res.data.songs[0].name
						this.name = res.data.songs[0].ar[0].name
					}
				})
				this.audio.play()
				this.audio.seek(0)
				this.audio.src = `http://music.163.com/song/media/outer/url?id=${this.allData[this.allIndex]}`
			},
			stop(){　//　播放、暂停事件
				this.play = !this.play
				if(this.play ){
					this.audio.play()
				}else{
					this.audio.pause()
				}
				this.audio.autoplay = false
			},
			next(){ // 下一页
				if(this.mode==2){
					this.allIndex = parseInt(Math.random()*this.allData.length)
				}else{
					if(this.allIndex==this.allData.length-1){
						this.allIndex = 0
					}else{
						this.allIndex = parseFloat(this.allIndex)+1
					}
				}
				uni.request({
					url:`http://musicapi.leanapp.cn/song/detail?ids=${this.allData[this.allIndex]}`,
					method: 'GET',
					success: res => {
						this.picUrl = res.data.songs[0].al.picUrl
						this.songName = res.data.songs[0].name
						this.name = res.data.songs[0].ar[0].name
					}
				})
				this.audio.play()
				this.audio.seek(0)
				this.audio.src = `http://music.163.com/song/media/outer/url?id=${this.allData[this.allIndex]}` // 音乐路径
			},
			tabSong(index){ // 列表切换歌曲
				if(this.allIndex != index){
					this.allIndex = index
					uni.request({
						url:`http://musicapi.leanapp.cn/song/detail?ids=${this.allData[index]}`,
						method: 'GET',
						success: res => {
							this.picUrl = res.data.songs[0].al.picUrl
							this.songName = res.data.songs[0].name
							this.name = res.data.songs[0].ar[0].name
						}
					})
					this.audio.play()
					this.audio.seek(0)
					this.audio.src = `http://music.163.com/song/media/outer/url?id=${this.allData[index]}` // 音乐路径
				}
			},
			chang(e){ // 拖动事件
				this.audio.seek(e.target.value)
				this.musicUseTimeMinutes = parseInt(e.target.value/60)
				this.musicUseTimeSecond = parseInt(((e.target.value/60)-parseInt(e.target.value/60))*60)
			}
		},
		onLoad(item) {
			// this.audio.destroy()
			uni.request({
				url:`http://musicapi.leanapp.cn/song/detail?ids=${item.id}`,
				method: 'GET',
				success: res => {
					this.picUrl = res.data.songs[0].al.picUrl
					this.songName = res.data.songs[0].name
					this.name = res.data.songs[0].ar[0].name
					console.log(this.picUrl,this.songName,this.name)
				}
			})
			this.listName = item.name.split(",")
			this.listSongName = item.songName.split(",")
			this.allData = item.allData.split(",")
			this.allIndex = item.index
			this.height = uni.getSystemInfoSync().windowHeight-75 // ---------------------------------------------------屏幕高
			this.audio = uni.createInnerAudioContext(); //--------------------------------------------------------------定义音频
			this.audio.autoplay = true // ------------------------------------------------------------------------------音乐自动播放
			this.audio.src = `http://music.163.com/song/media/outer/url?id=${this.allData[this.allIndex]}` // ----------音乐路径
			this.audio.onTimeUpdate(()=>{
				this.musicTime = parseInt(this.audio.duration)
				this.musicTimeMinutes = parseInt(this.audio.duration/60)
				this.musicTimeSecond = parseInt(((this.audio.duration/60)-parseInt(this.audio.duration/60))*60)
				this.musicUseTime = parseInt(this.audio.currentTime)
				this.musicUseTimeMinutes = parseInt(this.audio.currentTime/60)
				this.musicUseTimeSecond = parseInt(((this.audio.currentTime/60)-parseInt(this.audio.currentTime/60))*60)
			})
			this.audio.onSeeked(()=>{
				this.musicUseTime = parseInt(this.audio.currentTime)
				this.musicUseTimeMinutes = parseInt(this.audio.currentTime/60)
				this.musicUseTimeSecond = parseInt(((this.audio.currentTime/60)-parseInt(this.audio.currentTime/60))*60)
			})
			this.audio.onEnded(()=>{
				this.musicUseTime = 0
				this.musicUseTimeMinutes = 0
				this.musicUseTimeSecond = 0
				if(this.mode == 1){
					this.allIndex = this.allIndex-1
					this.next()
				}else{
					this.next()
				}
			})
		},
	}
</script>

<style scoped>
	.top{height: 65px;background-color: #31C27C;display: flex;position: relative;}
	.top>view:first-child{width: 25%;line-height: 85px;position: absolute;left: 0px;}
	.top>view:first-child image{width: 40px;height: 40px;margin-top: 25px;}
	.top>view:last-child{width: 100%;line-height: 85px;color: white;text-align: center;font-size: 30rpx;}
	
	.content{text-align: center;height: 1000rpx;background-size: 100% 100%;overflow: hidden;}
	.con_one{width:100%;height: 1000rpx;position: absolute;filter: blur(25px);z-index: -1;}
	.con_one image{width: 100%;height: 1000rpx;}
	
	.content .one1{padding-top: 30rpx;font-size: 40rpx;color: white;}
	.content .one2{padding-bottom: 30rpx;}
	.ball{width: 500rpx;height: 500rpx;overflow: hidden;margin: 60px auto;}
	.ball image{width:100%;height: 100%;border-radius: 50%;}
	
	.slider{display: flex;width: 100%;line-height: 40px;justify-content: center;color: #555555;margin-top: 80px;}
	.slider slider{width:70%;}
	
	.footer{width: 100%;height: 130rpx;display: flex;justify-content: space-between;position: fixed;bottom: 0px;z-index: 3;}
	.footer>.box{width:16%;height: 100%;display: flex;}
	
	.f_two>image,.f_fore>image{width: 80rpx;height: 80rpx;margin: auto;}
	.f_three>image{width: 120rpx;height: 120rpx;margin: auto;}
	.f_one>image,.f_five>image{width: 60rpx;height: 60rpx;margin: auto;}
	
	.list{position: fixed;width: 100%;height: 400rpx;background-color: rgba(0,0,0,.5);z-index: 2;bottom: 0px;padding-bottom: 130rpx;}
	.list>view{overflow: scroll;height: 400rpx;color: white;}
	.list .bgcolor{background-color: skyblue;border: 1px solid white;color: #D44439;}
	.list_one{display: flex;border-bottom: 1px solid #808080;line-height: 60rpx;}
	.list_one>view:first-child{text-align: right;padding-right: 30rpx;}
</style>
