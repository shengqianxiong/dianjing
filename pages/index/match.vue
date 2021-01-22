<template>
	<view class="match-mian">
		<view>
			<image :src="matchdata.coverImg" mode="" style="width: 100%;"></image>
		</view>
		<view class="match-title">
			<view class="title-one">{{matchdata.title}}</view>
			<view>{{matchdata.gameTypeName}} <text>|</text> <text v-if="matchdata.area==1">微信区</text><text v-if="matchdata.area==2">QQ区
				</text><text>|</text>{{matchdata.tag}}</view>
		</view>
		<view class="match-content" style="padding-bottom: 160rpx;"  v-if="matchdata.status == 1 && matchdata.eventStatus==1 && matchdata.isApply==2">
			<!-- 报名费 -->
			<view class="match-bg match-money">
				报名费用:¥{{matchdata.entryMoney}}
			</view>
			<!-- 比赛赛程 -->
			<view class="match-bg match-schedule">
				<view class="schedule-title">
					比赛赛程
				</view>
				<view class="schedule-con">
					<view class="schedule-item">
						<view class="number" :class="{ 'active': eventStatus == 1 }">1</view>
						<p>开始报名</p>
						<p>{{matchdata.entryTime}}</p>
					</view>
					<view class="schedule-item">
						<view class="number" :class="{ 'active': eventStatus == 2 }">2</view>
						<p>预计开赛时间</p>
						<p>{{matchdata.startTime}}</p>
					</view>
					<view class="schedule-item">
						<view class="number">3</view>
						<p>比赛结束</p>
						<p>{{matchdata.endTime}}</p>
					</view>
				</view>
			</view>
			<!-- 比赛奖励 -->
			<view class="match-bg">
				<view class="schedule-title">
					比赛奖励
				</view>
				<view class="award">
					<view v-for="(item,index) in matchdata.gameAwardList" :key="index" style="display: block;">
						<text>{{item.title}}: </text> <text>{{item.content}}</text>
					</view>
				</view>
			</view>
			<!-- 参赛选手 -->
			<view class="match-bg">
				<view class="schedule-title" style="display: flex;">
					<view>参赛选手</view>
					<view style="font-size: 30rpx;text-align: right;flex: 1;"><text>参赛人数:</text>{{matchdata.applyNumber}}/{{matchdata.needNumber}}</view>
				</view>
				<view class="award" style="display: flex;flex-wrap: wrap;">
					<view class="award-item" v-for="(item,index) in matchdata.gameApplyList" :key="index">
						<image v-if="item.headImg == null" src="../../static/img/my/avatar.png"></image>
						<image v-else :src="item.headImg"></image>
						<view class="award-text">{{item.nickName}}</view>
						<view style="color: #ffffff;margin-top: 10rpx;">报名时间:{{item.createTime}}</view>
					</view>
				</view>
			</view>
			<!-- 比赛规则 -->
			<view class="match-bg">
				<view class="schedule-title">
					比赛规则
				</view>
				<view class="schedule-rule" v-html="matchdata.detail">

				</view>
			</view>
			<!-- 立即报名 -->
			<view class="footer-btn"  v-if="matchdata.status == 1 && matchdata.eventStatus==1 && matchdata.isApply==2">
				<view class="share-btn">
					<!-- #ifdef APP-PLUS -->
					<image src="../../static/img/index/share.png" style="margin-left: 40rpx;"  @click="shareWeiXin"></image>
					<!-- #endif -->
					<view class="name" @click="open(matchdata.status,matchdata.entryTime)">立即报名</view>
				</view>
			</view>
		</view>
		<view class="match-content" style="padding-bottom:40rpx;"  v-else>
			<!-- 报名费 -->
			<view class="match-bg match-money">
				报名费用:¥{{matchdata.entryMoney}}
			</view>
			<!-- 比赛赛程 -->
			<view class="match-bg match-schedule">
				<view class="schedule-title">
					比赛赛程
				</view>
				<view class="schedule-con">
					<view class="schedule-item">
						<view class="number" :class="{ 'active': eventStatus == 1 }">1</view>
						<p>开始报名</p>
						<p>{{matchdata.entryTime}}</p>
					</view>
					<view class="schedule-item">
						<view class="number" :class="{ 'active': eventStatus == 2 }">2</view>
						<p>预计开赛时间</p>
						<p>{{matchdata.startTime}}</p>
					</view>
					<view class="schedule-item">
						<view class="number">3</view>
						<p>比赛结束</p>
						<p>{{matchdata.endTime}}</p>
					</view>
				</view>
			</view>
			<!-- 比赛奖励 -->
			<view class="match-bg">
				<view class="schedule-title">
					比赛奖励
				</view>
				<view class="award">
					<view v-for="(item,index) in matchdata.gameAwardList" :key="index" style="display: block;">
						<text>{{item.title}}: </text> <text>{{item.content}}</text>
					</view>
				</view>
			</view>
			<!-- 参赛选手 -->
			<view class="match-bg">
				<view class="schedule-title" style="display: flex;">
					<view>参赛选手</view>
					<view style="font-size: 30rpx;text-align: right;flex: 1;"><text>参赛人数:</text>{{matchdata.applyNumber}}/{{matchdata.needNumber}}</view>
				</view>
				<view class="award" style="display: flex;flex-wrap: wrap;">
					<view class="award-item" v-for="(item,index) in matchdata.gameApplyList" :key="index">
						<image v-if="item.headImg == null" src="../../static/img/my/avatar.png"></image>
						<image v-else :src="item.headImg"></image>
						<view class="award-text">{{item.nickName}}</view>
						<view style="color: #ffffff;margin-top: 10rpx;">报名时间:{{item.createTime}}</view>
					</view>
				</view>
			</view>
			<!-- 比赛规则 -->
			<view class="match-bg">
				<view class="schedule-title">
					比赛规则
				</view>
				<view class="schedule-rule" v-html="matchdata.detail">
		
				</view>
			</view>
			<!-- 立即报名 -->
			<view class="footer-btn"  v-if="matchdata.status == 1 && matchdata.eventStatus==1 && matchdata.isApply==2">
				<view class="share-btn">
					<!-- #ifdef APP-PLUS -->
					<image src="../../static/img/index/share.png" style="margin-left: 40rpx;"  @click="shareWeiXin"></image>
					<!-- #endif -->
					<view class="name" @click="open(matchdata.status,matchdata.entryTime)">立即报名</view>
				</view>
			</view>
		</view>
		<!-- 关联游戏账号弹框 -->
		<uni-popup ref="popup1" type="center">
			<view  style="background-color: #ffffff;width:600rpx; height:620rpx;border-radius: 12rpx;padding: 20rpx;">
				<view>
					<view class="title" style="font-size: 36rpx;color: #333333;text-align: center;">
						关联游戏账号
						<view style="font-size: 28rpx;margin: 20rpx 0;">请填写您<text style="color: #FF9D3B;" v-if="matchdata.area==1">微信区</text><text
							 style="color: #FF9D3B;" v-if="matchdata.area==2">QQ区</text>的游戏昵称和 <text v-if="matchdata.area==2">QQ号</text><text v-if="matchdata.area==1">微信号</text></view>
					</view>
					<view class="cu-form-group inputone">
						<input  v-model="nickName" placeholder="请输入您的游戏昵称" />
					</view>
					<view class="cu-form-group" v-if="matchdata.area==1">
						<input  v-model="gameNumber" placeholder="请输入您的微信号" />
					</view>
					<view class="cu-form-group" v-if="matchdata.area==2">
						<input  v-model="gameNumber" placeholder="请输入您的QQ号" />
					</view>
				</view>
				<view class="footer-btn2">
					<view class="gamebind-btn" @click="bound">
						绑定并保存
					</view>
				</view>
			</view>
		</uni-popup>
		<uni-popup ref="popup" type="bottom">
			<view style="background-color: #FFFFFF;padding:20rpx 0; border-top-left-radius:20rpx;border-top-right-radius:20rpx;">
				<view style="width: 100%;text-align: center;font-size:28rpx;">
					支付费用
				</view>
				<view class="popup-item">
					游戏名称 <text style="margin-left: 50rpx;">{{matchdata.gameTypeName}}</text>
				</view>
				<view class="popup-item">
					游戏昵称 <text style="margin-left: 50rpx;">{{nickName}}</text>
				</view>
				<view class="popup-item" v-if="matchdata.area==1">
					微信号 <text style="margin-left: 76rpx;">{{gameNumber}}</text>
				</view>
				<view class="popup-item" v-if="matchdata.area==2">
					QQ号 <text style="margin-left: 92rpx;">{{gameNumber}}</text>
				</view>
				<view class="popup-item">
					赛事名称 <text style="margin-left: 50rpx;">{{matchdata.title}}</text>
				</view>
				<view class="popup-item">
					报名费用 <text style="margin-left: 50rpx;color: #f2ad44;">{{matchdata.entryMoney}}</text>
				</view>
				<view class="popup-item">
					支付方式 <text style="margin-left: 50rpx;">零钱支付</text>    <text style="float: right;margin-right: 20rpx;">余额: <text style="color: #f2ad44;">{{money}}</text> </text>
				</view>
				<view style="padding: 20rpx;font-size: 28rpx;color: #333333;">
					<text> 报名成功后，非特殊原因报名费用不予退还</text>
				</view>
				<view class="footer-btn2">
					<view class="gamebind-btn" @click="payment">
						立即支付
					</view>
				</view>
			</view>
		</uni-popup>
	</view>

</template>

<script>
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupMessage from '@/components/uni-popup/uni-popup-message.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	import appShare from '@/utils/share.js'
	export default {
		data() {
			return {
				slice: '',
				area: '',
				gameTypeId: '',
				nickName: '',
				gameNumber: '',
				gameEventId: '',
				title: '',
				area: '',
				tag: '',
				headImg: '',
				money:'',
				entryMoney: '',
				eventStatus: '',
				matchdata: {}
			};
		},
		components: {
			uniPopup,
			uniPopupMessage,
			uniPopupDialog
		},
		onLoad(e) {
			this.matchdetail(e.id,e.userId)
			let userId = this.$queue.getData("userId");
			this.getUserInfo(userId)
		},
		methods: {
			open(status,entryTime) {
				if(status == 2){
					this.$queue.showToast('赛事已停止报名');
					return
				}
				if (Date.parse(entryTime) > new Date()) {
				    this.$queue.showToast('未开始报名');
				    return
				}else{
					let userId = this.$queue.getData("userId");
					this.$Request.getT(`/gameAccount/findAccount?userId=${userId}&gameTypeId=${this.gameTypeId}&area=${this.area}`).then(
					res => {
						if (res.data === null) {
							this.$refs.popup1.open()
						} else {
							this.$refs.popup.open()
							this.gameNumber = res.data.gameNumber
							this.nickName = res.data.nickName
						}
					});
				}
			},
			// 立即支付
			payment() {
				if (this.money < this.entryMoney) {
					this.$queue.showToast('余额不足，请前往我的钱包页面充值！');
				}
				let userId = this.$queue.getData("userId");
				let userName = this.$queue.getData("userName");
				let phone = this.$queue.getData("phone");
				this.$Request.postJson('/gameApply/save', {
						userId: userId,
						userName: userName,
						phone: phone,
						gameNumber: this.gameNumber,
						nickName: this.nickName,
						gameTypeId: this.gameTypeId,
						gameEventId: this.gameEventId,
						title: this.title,
						area: this.area,
						tag: this.tag,
						headImg: this.headImg,
						entryMoney: this.entryMoney,
						eventStatus: this.eventStatus
					})
					.then(res => {
						if (res.status === 0) {
							this.$refs.popup.close();
							this.$queue.showToast('报名成功');
							uni.navigateTo({
								url: '/pages/my/game'
							});
						} else {
							this.$queue.showToast(res.msg);
							uni.hideLoading();
						}
					});
			},
			// 分享
			shareWeiXin() {
				let shareData = {
					shareUrl: 'http://www.baidu.com',
					shareTitle: '邀请你加入省钱兄电竞！',
					shareContent: '邀请码：212，点击进入，在省钱兄中和我一起做玩游戏！',
					shareImg: this.$queue.publicYuMing() + '/logo.png',
					type: 0
				};
				appShare(shareData, res => {
					console.log('分享成功回调', res);
				});
			},
			// 详情获取
			matchdetail(id,userId) {
				uni.showLoading({
					title: '加载中...'
				});
				this.$Request.getT(`/gameEvent/find?id=${id}&userId=${userId}`).then(res => {
					if (res.status === 0) {
						this.matchdata = res.data
						this.area = res.data.area
						this.gameTypeId = res.data.gameTypeId
						this.gameNumber = res.data.gameNumber
						this.gameEventId = res.data.id
						this.title = res.data.title
						this.area = res.data.area
						this.tag = res.data.tag
						this.entryMoney = res.data.entryMoney
						this.eventStatus = res.data.eventStatus
						uni.hideLoading();
					}
				});
			},
			//获取用户信息
			getUserInfo(userId) {
				this.$Request.getT('/user/find?id=' + userId).then(res => {
					if (res.status === 0) {
						this.money = res.data.money
						this.headImg = res.data.headImg
					} else {
						this.$queue.logout();
					}
				});
			},
			//绑定账号操作
			bound() {
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
								area: this.area,
								gameTypeId: this.gameTypeId,
								nickName: this.nickName,
								gameNumber: this.gameNumber
							}).then(res => {
								if (res.status === 0) {
									this.$queue.showToast('绑定成功');
									this.$refs.popup1.close();
								} else {
									this.$queue.showToast(res.msg);
								}
							});
						}
					}
				});
			}
		}
	}
</script>

<style>
	page {
		background-color: #EDEDED;
	}
	.match-title {
		width: 100%;
		color: #333333;
		text-align: center;
		background-color: #ffffff;
		padding: 44rpx 0;
		margin-top:10rpx;
		font-size: 32rpx;
	}

	.match-title .title-one {
		font-size: 38rpx;
		margin-bottom: 44rpx;
		font-weight: 600;

	}

	.match-title text {
		margin: 0 22rpx;
	}

	.match-content .match-bg {
		background: #303030;
		border-radius: 12rpx;
		margin: 26rpx 20rpx;
		padding: 24rpx;
		color: #ffffff;
	}

	.cu-form-group {
		background: #303030;
		color: #ffffff;
		margin: 0 20rpx;
		border-radius: 12rpx;
	}

	.cu-form-group.inputone {
		margin-bottom: 45rpx;
	}

	.cu-form-group input {
		color: #ffffff;
	}

	.footer-btn2 {
		margin-top: 20rpx;
	}

	.footer-btn2 .gamebind-btn {
		margin: 0 60rpx;
		padding: 30rpx 0;
		text-align: center;
		color: #FFFFFF;
		font-size: 30rpx;
		border-radius: 40rpx;
		background: #ff9e3c;
	}

	.match-content .match-bg.match-money {
		text-align: center;
		color: #ff9c3b;
		font-size: 28rpx;
	}

	.schedule-title {
		border-bottom: 1px solid #ECECEC;
		padding-bottom: 22rpx;
		font-size: 32rpx;
	}

	.schedule-con {
		display: flex;
		margin-top: 22rpx;
	}

	.schedule-con .schedule-item {
		flex: 1;
		text-align: center;
	}

	.schedule-con .schedule-item:last-child .number::after {
		display: none;
	}

	.schedule-con .schedule-item .number.active::after {
		background: #FF9D3B;
	}

	.schedule-con .schedule-item .number {
		width: 76rpx;
		height: 76rpx;
		margin: 0 auto;
		line-height: 80rpx;
		border-radius: 50%;
		background-color: #808080;
		position: relative;
	}

	.schedule-con .schedule-item .number::after {
		content: '';
		position: absolute;
		top: 52%;
		left: 120%;
		background: #ffffff;
		width: 110rpx;
		height: 2rpx;
	}

	.popup-item {
		border-bottom: 2rpx solid #ECECEC;
		padding: 20rpx 0rpx;
		margin-left: 30rpx;
		font-size: 30rpx;
		color: #333333;
	}

	.schedule-con .schedule-item .number.active {
		background-color: #FF9D3B;
	}

	.schedule-con .schedule-item p {
		margin-top: 34rpx;
	}

	.award {
		margin-top: 30rpx;
		color: #ffa03b;
		font-size: 28rpx;
		width: 100%;
	}

	.award .award-item {
		width: 32%;
		text-align: center;
		margin-bottom: 10rpx;
	}

	.award .award-item image {
		width: 100rpx;
		height: 100rpx;
	}

	.award-text {
		text-align: center;
		color: #ffffff;
		margin-top: 10rpx;
	}

	.schedule-rule {
		margin-top: 30rpx;
		color: #ffffff;
		font-size: 28rpx;
	}

	.footer-btn .share-btn {
		display: flex;
		align-items: center;
	}

	.footer-btn {
		position: fixed;
		bottom: 0;
		left: 0;
		right: 0;
		padding: 40rpx 0;
		background-color: #ffffff;
	}

	.footer-btn .share-btn image {
		width: 56rpx;
		height: 56rpx;
	}

	.footer-btn .share-btn .name {
		flex: 1;
		font-size: 34rpx;
		text-align: center;
		background-color: #ff9e3c;
		width: 480rpx;
		color: #ffffff;
		float: right;
		margin: 0 60rpx;
		padding:20rpx 0;
		border-radius: 46rpx;
	}
</style>
