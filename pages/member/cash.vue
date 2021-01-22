<template>
	<view class="cash">
		<view style="background-image: url(../../static/img/my/cashbackground.png);background-size: 100%;height: 400upx;">
			<view style="font-size: 16px;color: #FFFFFF;padding-top: 100upx;">可提现总额</view>
			<view style="font-size: 29px;color: #FFFFFF;padding-top: 20upx;">¥ {{mayMoney}}</view>

			<view style="width: 90%;height: max-content;margin-left: 40upx;background-color: #FFFFFF;box-shadow: rgba(183, 183, 183, 0.3) 0px 1px 10px;margin-top: 50upx;border-radius: 20upx;">
				<view style="display: flex;flex-direction: row;padding: 20upx;">
					<view style="font-size: 16px;color: #333333;">提现金额</view>
					<view style="font-size: 11px;color: #333333;margin-left: 20upx;margin-top: 10upx;">{{min}}{{value}}元</view>
					<!-- <view style="font-size: 11px;color: #333333;margin-left: 20upx;margin-top: 10upx;">{{money}}{{value1}}元</view> -->
				</view>
				<view style="display: flex;flex-direction: row;padding: 20upx;">
					<view style="font-size: 14px;color: #333333;">¥</view>
					<input type="number" v-model="money" placeholder="请输入金额" style="font-size: 14px;color: #333333;text-align: left;margin-left: 10upx;width: 100%;" />
				</view>
				<view style="background: #E6E6E6;width: 100%;height: 1upx;"></view>

				<view style="display: flex;flex-direction: row;flex-wrap: wrap;">
					<view style="display: flex;flex-direction: row;" v-for="(item, index) in moneyList" :key="index">
						<view>
							<view style="padding: 20upx;" @click="getOut1(item.money)">
								<view style="padding-top: 40upx;width: 180upx; height: 120upx;background-color: #FFFFFF;border:1px solid #FF332F;border-radius: 10upx;">
									{{ item.money }}
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>
			<view @click="getOut" v-if="mayMoney !== '0'" style="margin: 32upx;font-size: 18px;background: #ff9e3c;color: white;border-radius: 10px;height: 40px;line-height: 40px">
				提现
			</view>

			<view style="display: flex;width: 100%;justify-content: center;">
				<view style="color: grey;padding-bottom: 30px;padding-top: 20upx;" @click="gozhiFuBao">提现账号设置</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				money: '',
				zhiFuBao: '',
				mayMoney: '0',
				zhiFuBaoName: '',
				moneyList: [
					{
						id:0,
						money:10
					},
					{
						id:1,
						money:20
					},
					{
						id:2,
						money:50
					},
					{
						id:3,
						money:100
					},{
						id:4,
						money:150
					},
					{
						id:5,
						money:200
					}
				],
				value: 0,
				value1:0,
				min: ''
			};
		},
		onShow: function(e) {
			this.getMoney();
			this.getasdas();
		},
		onNavigationBarButtonTap() {
			this.list();
		},
		methods: {
			getasdas() {
				this.$Request.getT('/common/type/112').then(res => {
					if (res.status === 0) {
						this.min = res.data.min;
						this.value = res.data.value;
					}
				});
				this.$Request.getT('/common/type/111').then(res => {
					if (res.status === 0) {
						this.value1 = res.data.value;
					}
				});
			},
			list() {
				uni.navigateTo({
					url: '/pages/member/cashList'
				});
			},
			gozhiFuBao() {
				uni.navigateTo({
					url: '/pages/member/zhifubao'
				});
			},
			getMoney() {
				let that = this;
				let userId = this.$queue.getData('userId');
				if (userId) {
					//this.$queue.showLoading("加载中...");
					//可以提现金额查询预估收入查询
					that.$Request.getT('/user/find?id=' + userId).then(res => {
						if (res.status === 0 && res.data) {
							that.mayMoney = res.data.money;
						} else if (res.status === -102) {
							that.$queue.showToast(res.msg);
							that.$queue.logout();
							uni.navigateTo({
								url: '/pages/public/login'
							});
						} else {
							that.mayMoney = '0';
							that.$queue.showToast(res.msg);
						}
					});
					that.$Request.getT('/user/find?id=' + userId).then(res => {
						if (res.status === 0 && res.data) {
							that.zhiFuBao = res.data.zhiFuBao;
							that.zhiFuBaoName = res.data.zhiFuBaoName;
						}
						uni.hideLoading();
					});
				}
			},
			getOut() {
				let that = this;
				let userId = this.$queue.getData('userId');
				if (userId) {
					if (that.zhiFuBao && that.zhiFuBaoName) {
						if (!/^\d+$/.test(that.money)) {
							uni.showToast({
								icon: 'none',
								title: '请输入正确金额，不能包含中文，英文，特殊字符和小数'
							});
							return;
						}
						let moneynum = that.money*that.value1
						let money = parseInt(that.money) + moneynum
						if (parseFloat(that.money).toFixed(1) >= that.value) {
							uni.showModal({
								title: '提现申请提示',
								content: '请仔细确认收款人信息\n\n姓名:' + that.zhiFuBaoName + '\t\t金额:' + money + '\n\n\(包含服务费 ¥' + moneynum + ')' + '\n\n收款账号：' + that.zhiFuBao + '',
								success: e => {
									if (e.confirm) {
										that.$queue.showLoading('提现中...');
										that.$Request.getT('/cash/cashMoney?userId=' + userId + '&money=' + that.money).then(res => {
											if (res.status === 0) {
												that.$queue.showToast('提现申请成功，预计三个工作日到账');
												that.getMoney();
											} else {
												uni.showModal({
													title: '温馨提示',
													content: res.msg,
													showCancel: false,
													cancelText: '取消',
													confirmText: '确认'
												});
											}
											uni.hideLoading();
										});
									}
								}
							});
						} else {
							that.$queue.showToast('提现金额必须大于或等于10元才可提现');
						}
					} else {
						uni.navigateTo({
							url: '/pages/member/zhifubao'
						});
					}
				} else {
					uni.navigateTo({
						url: '/pages/public/login'
					});
				}
			},
			getOut1(money) {
				let that = this;
				let userId = this.$queue.getData('userId');
				if (userId) {
					if (that.zhiFuBao && that.zhiFuBaoName) {
						let moneynum = money*that.value1
						let moneyone = parseInt(money) + moneynum
						if (parseFloat(money).toFixed(1) >= 10) {
							uni.showModal({
								title: '提现申请提示',
								content: '请仔细确认收款人信息\n\n姓名:' + that.zhiFuBaoName + '\t\t金额:' + moneyone + '\n\n\(包含服务费 ¥' + moneynum + ')' + '\n\n收款账号：' + that.zhiFuBao + '',
								success: e => {
									if (e.confirm) {
										that.$queue.showLoading('提现中...');
										that.$Request.getT('/cash/cashMoney?userId=' + userId + '&money=' + money).then(res => {
											if (res.status === 0) {
												that.$queue.showToast('提现申请成功，预计三个工作日到账');
												that.getMoney();
											} else {
												uni.showModal({
													title: '温馨提示',
													content: res.msg,
													showCancel: false,
													cancelText: '取消',
													confirmText: '确认'
												});
											}
											uni.hideLoading();
										});
									}
								}
							});
						} else {
							that.$queue.showToast('提现金额必须大于或等于10元才可提现');
						}
					} else {
						uni.navigateTo({
							url: '/pages/member/zhifubao'
						});
					}
				} else {
					uni.navigateTo({
						url: '/pages/public/loginphone'
					});
				}
			}
		}
	};
</script>

<style lang="less">
	@import '../../static/css/index.css';

	.view2-view-text {
		font-size: 14px;
		color: #000000;
		margin-left: 20upx;
		width: 80%;
	}

	.view2-view-image-right {
		width: 18upx;
		height: 30upx;
		margin-left: 50upx;
	}

	.cash {
		text-align: center;
		background: white;
		height: 100%;
		position: absolute;
		width: 100%;

		.cash-top {
			padding: 32upx 32upx 50upx 32upx;
			/* border-bottom: 1px solid gainsboro; */
			background: #e10a07;
		}

		.leiji {
			font-size: 14px;
			color: #ffffff;
			margin-bottom: 10px;
		}
	}
</style>
