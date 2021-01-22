<template>
	<view class="game-main">
		<view class="game-item" v-for="(item, index) in gamelistdata" :key="index">
			<view class="game-img">
				<image :src="item.gameType.img" mode=""></image>
				<view style="text-align: center;color: #f2ad44;">{{item.gameType.name}}</view>
			</view>
			<view class="game-right">
				<view class="right-item">
					微信区
					<view class="right-btn" v-if="item.status == 1" @click="binding(item)">未绑定</view>
					<view class="right-btn" v-else  style="background: #303030;" @click="binding(item)">已绑定</view>
				</view>
				<view class="right-item">
					QQ区
					<view class="right-btn" v-if="item.status1 == 1" @click="binding1(item)">未绑定</view>
					<view class="right-btn" v-else style="background: #303030;" @click="binding1(item)">已绑定</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
		export default {
			data() {
				return {
					userId:'',
					userName:'',
					phone: '',
					district:'',
					headImg: '/static/logo.png',
					gamelistdata:[]
				};
			},
			onLoad() {
				this.gameList()
			},
			methods: {
				// 微信区
				binding(item){
					if(item.status == 1){
						this.district = '微信区'
						uni.navigateTo({
							url:'/pages/my/gamebinding?name=' + item.gameType.name +'&district=' + this.district + '&area='+item.area + '&gameTypeId='+item.gameType.id
						})
					}else{
						uni.showModal({
							title: '提示',
							content: '是否解绑',
							success: e => {
								if (e.confirm) {
									this.$Request.getT("/gameAccount/delete", {
										id: item.gameAccountId,
									}).then(res => {
										setTimeout(() => {
											uni.showToast({
												icon: 'none',
												title: '解绑成功'
											});
										}, 200);
										this.gameList()
									});
								}
							}
						});
					}
				},
				// qq区
				binding1(item){
					if(item.status1 == 1){
						this.district = 'QQ区'
						uni.navigateTo({
							url:'/pages/my/gamebinding?name=' + item.gameType.name +'&district=' + this.district + '&area='+item.area1 + '&gameTypeId='+item.gameType.id
						})
					}else{
						uni.showModal({
							title: '提示',
							content: '是否解绑',
							success: e => {
								if (e.confirm) {
									this.$Request.getT("/gameAccount/delete", {
										id: item.gameAccountId1,
									}).then(res => {
										if (res.status === 0) {
											this.$queue.showToast('解绑成功');
											this.gameList()
										} else {
											this.$queue.showToast(res.msg);
										}
									});
								}
							}
						});
					}
				},
				// 游戏账号列表
				gameList(){
					let userId = this.$queue.getData("userId");
					this.$Request.getT("/gameAccount/list", {
						userId: userId,
					}).then(res => {
						if (res.status === 0) {
							this.gamelistdata = res.data
						} else {
							this.$queue.showToast(res.msg);
						}
					});
				}
			}
		};
</script>

<style>
	page{
		background-color: #EDEDED;
	}
	.game-main{
		padding: 40rpx 10rpx;
	}
	.game-main .game-item{
		display: flex;
		width: 100%;
		background-color: #ffffff;
		padding: 42rpx 26rpx;
		border-radius: 16rpx;
		margin-bottom: 20rpx;
	}
	.game-main .game-item .game-img image{
		width: 110rpx;
		height: 110rpx;
		border-radius: 22rpx;
		text-align: center;
	}
	.game-right{
		flex: 1;
		margin-left:52rpx;
	}
	.game-right .right-item{
		font-size: 28rpx;
		padding: 16rpx 0 20rpx 0;
		border-bottom: 1px solid #ECECEC;
	}
	.game-right .right-item .right-btn{
		color: #ffffff;
		background-color: #f2ad44;
		border-radius:12rpx;
		padding:10rpx 16rpx;
		font-size: 26rpx;
		float: right;
		position: relative;
		top: -8rpx;
	}
</style>
