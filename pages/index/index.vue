<template>
	<view>
		<view class="mid" @click="possible=false" v-if="possible">
			<view>
				<view @click.stop="caly(item)" v-if="con==''" v-for="(item,index) in RM" :key="index">
					{{item.first}}
				</view>
				
				<view @click="calyCall(item)" v-if="con!=''" v-for="(item,index) in SS" :key="index">
					<view>{{item.name}}</view>
					<view>{{item.artists[0].name}}</view>
				</view>
			</view>
		</view>
		<view class="header">
			<view class="ala">
				<view class="yy">
					<view class="inp">
						<input @focus="light" @input="lood" type="text" v-model="con" />
					</view>
				</view>
			</view>
				
			<view>
				<view :class="bg==0?'bg':''" @click="bg=0"><text>推荐</text></view>
				<view :class="bg==1?'bg':''" @click="bg=1"><text>热歌榜</text></view>
				<view :class="bg==2?'bg':''" @click="bg=2"><text>歌手</text></view>
			</view>
		</view>
		<view v-if="bg==0">
			<PageFirst></PageFirst>
		</view>
		<view v-if="bg==1">
			<hot></hot>
		</view>
		<view v-if="bg==2">
			<song></song>
		</view>
	</view>
</template>

<script>
	import PageFirst from '../pageFirst/pageFirst';
	import hot from '../hot/hot';
	import song from '../song/song';
	export default {
		data(){
			return{
				bg:0,
				con:"",
				RM:[],
				SS:[],
				possible:false,
				allData:[],
				songName:[],
				name:[],
				index:0
			}
		},
		onLoad() {
			uni.request({ // 热门搜索
				url: `http://musicapi.leanapp.cn/search/hot/detail`,
				method: 'GET',
				success: res => {
					this.RM = res.data.result.hots
				},
			})
		},
		components:{
			PageFirst,
			hot,
			song
		},
		methods:{
			light(){ // 聚焦
				this.possible = true
			},
			cli(){ // 搜索
				// console.log(this.con)
			},
			lood(){ // 键盘
				if(this.con!=""){
					uni.request({ // 热门搜索
						url: `http://musicapi.leanapp.cn/search/suggest?keywords= ${this.con}`,
						method: 'GET',
						success: res => {
							this.SS = res.data.result.songs
							console.log(res)
						},
					})
				}
			},
			caly(data){
				this.con = data.first
			},
			calyCall(data){
				console.log(data)
				this.con = data.name
				this.allData = data.id
				this.songName = data.name
				this.name = data.artists[0].name
				uni.navigateTo({
					url:`../palyList/palyList?id=${data.id}&allData=${this.allData}&index=${this.index}&songName=${this.songName}&name=${this.name}`
				})
			}
		}
	}
</script>

<style scoped>
	page{height: 100%;}
	.header{height: 105px;background-color: #31C27C;}
	.ala{height:40px;}
	.yy{background-color: #31C27C;height: 60px;position: fixed;z-index: 5;width:100%;}
	.inp{margin-top: 25px;width:60%;height:30px;border: 1px #2AA56A solid;border-radius: 30px;margin-bottom: 9px;margin-left: 5%;position: relative;z-index: 5;}
	.inp input{margin-top: 4px;margin-left: 11px;width: 81%;color: green;}
	/* .inp text{position:absolute;top:4px;right: 5px;color: #FF2424;border: 1px solid #FF2424;border-radius: 50%;text-align: center;width:20px;line-height: 20px;} */
	.header>view:last-child{margin-top: 25px;background-color: white;display: flex;justify-content: space-around;height: 40px;}
	.header>view:last-child view{text-align: center;color:#7D7E80;margin: 0px;line-height: 38px;text-align: center;border-bottom: 2px solid white;width: 33.33%;}
	.header view:last-child .bg>text{border-bottom: 2px solid white;padding: 0px 4px;}
	.header view:last-child .bg>text{color: #dd001b;border-bottom-color:#dd001b;display: inline-block;}
	
	.mid{width:100%;height: 100%;background:rgba(0,0,0,.7);position: fixed;top: 0px;left: 0px;z-index: 2;}
	.mid>view{width:60%;margin-top: 63px;color: white;margin-left: 7%;}
	.mid>view>view{border-bottom: 1px solid #EBEBEA;padding: 5px 0px;margin-bottom: 5px;display: flex;justify-content: space-between;}
	.mid>view>view>view:first-child{overflow: hidden;text-overflow: ellipsis;width:70%;height: 20px;}
	.mid>view>view>view:last-child{white-space: nowrap;overflow: hidden;text-overflow: ellipsis;width:20%;height: 20px;}
</style>
