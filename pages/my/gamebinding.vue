<template>
	<view class="gamebind-main">
		<view class="gamebind-content">
			<view class="gamebind-title">
				<text>当前绑定游戏</text>
				<text style="float: right;">{{gameName}}</text>
			</view>
			<view class="gamebind-title">
				<text>当前绑定区服</text>
				<text style="float: right;">{{district}}</text>
			</view>
			<view class="cu-form-group inputone">
				<input v-model="nickName" placeholder="请输入您的游戏昵称"/>
			</view>
			<view class="cu-form-group" v-if="district=='微信区'">
				<input v-model="gameNumber" placeholder="请输入您的微信号"/>
			</view>
			<view class="cu-form-group" v-if="district=='QQ区'">
				<input v-model="gameNumber" placeholder="请输入您的QQ号"/>
			</view>
			<view style="color:#e70805;font-size: 28rpx;margin-top: 62rpx;line-height:50rpx;">
				温馨提示：如在省钱兄电竞绑定的<text v-if="district=='微信区'">微信号</text><text v-if="district=='QQ区'">QQ号</text>与实际参赛<text v-if="district=='微信区'">微信号</text><text v-if="district=='QQ区'">QQ号</text>的不一致，将会导致赛事不能结算，请注意填写。
			</view>
		</view>
		<view class="footer-btn"> 
			<view class="gamebind-btn" @click="bound">
				完成绑定
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				nickName:'',
				gameNumber:'',
				gameName:'',
				district:'',
				area:'',
				gameTypeId:''
			};
		},
		onLoad(e) {
			this.gameName = e.name
			this.district = e.district
			this.area = e.area
			this.gameTypeId = e.gameTypeId
		},
		methods: {
			//绑定账号操作
			bound(){
				if(this.nickName == ''){
					this.$queue.showToast('请输入游戏昵称');
					return
				}
				if(this.gameNumber == ''){
					this.$queue.showToast('请输入游戏编号');
					return
				}
				uni.showModal({
					title: '提示',
					content: '是否绑定',
					success: e => {
						if (e.confirm) {
							let userId = this.$queue.getData("userId");
							this.$Request.postJson("/gameAccount/save", {
								userId: userId,
								area:this.area,
								gameTypeId:this.gameTypeId,
								nickName:this.nickName,
								gameNumber:this.gameNumber
							}).then(res => {
								if (res.status === 0) {
									uni.navigateTo({
										url: '/pages/my/gamenumber'
									});
								} else {
									this.$queue.showToast(res.msg);
								}
							});
						}
					}
				});
			}
		}
	};
</script>

<style>
	page{
		background: #EDEDED;
	}
	/*www.xiansqx.com版权所有*/
	.gamebind-content{
		background-color: #FFFFFF;
		margin: 20rpx;
		border-radius: 16rpx;
		padding: 60rpx 42rpx;
	}
	.gamebind-title{
		border-bottom: 1px solid #F0F0F0;
		padding-bottom: 36rpx;
		margin-bottom: 42rpx;
	}
	.cu-form-group{
		background: #303030;
		color: #ffffff;
	}
	.cu-form-group.inputone{
		margin-bottom:45rpx;
	}
	.cu-form-group input{
		color: #ffffff;
	}
	.footer-btn{
		margin-top: 72rpx;
	}
	.footer-btn .gamebind-btn{
		margin: 0 60rpx;
		padding: 30rpx 0;
		text-align: center;
		color: #FFFFFF;
		font-size:30rpx;
		border-radius: 40rpx;
		background: #ff9e3c;
	}
</style>
