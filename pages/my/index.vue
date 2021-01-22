<template>
	<view class="content" style="color: #F2F2F2;background-color: #ededed;">
		<view class="view1">
			<view class="login-view" @click="goLogin">
				<view style="width: 100%; display: flex;flex-direction: row;align-items: center;">
					<image :src="headImg" style="border-radius: 100upx;width: 130upx;height: 130upx; margin-left: 30upx;" @click="uploadImg()"></image>
					<view class="login-view-text1" style="flex: 1;margin-left: 27rpx;">
						<view style="color: #FFFFFF;">{{ userName  ? userName  : '游客' }}</view>
						<view v-if="phone" style="color: #fba513; font-size: 12px;margin-top: 10upx;">{{phone.substr(0,4)}}****{{phone.substr(8,11)}}</view>
					</view>
					<view class=""  @click="statuselect()" style="float: right;margin-right:6rpx;padding: 6rpx 20rpx;border-radius:16rpx; background-color: rgba(255,255,255,0.2);">
						<view style="color: #ffffff;">个人资料</view>
					</view>
				</view>
			</view>
		</view>
		<view style="position: relative;top:-140rpx;">
			<view class="view2">
				<view class="view2-title">
					<view>我的比赛</view>
					<view @click="gomygame" style="color: #979797;font-size: 28rpx;flex: 1;text-align: right;">查看全部比赛 <image src="../../static/img/my/look.png" style="width: 12rpx;height: 22rpx;margin-left: 16rpx;position: relative;top:2rpx;" mode=""></image></view>
				</view>
				<view class="view2-main">
					<view class="view2-item" @click="gomygame1(1)">
						<image src="../../static/img/my/start.png" style="width: 49rpx;height: 49rpx;" mode=""></image>
						<text class="text-num" v-if="countdata.noStart !== 0">{{countdata.noStart}}</text>
						<view class="item-text">
							待开始
						</view>
					</view>
					<view class="view2-item" @click="gomygame1(2)">
						<image src="../../static/img/my/proceed.png" style="width: 49rpx;height: 49rpx;" mode=""></image>
						<text class="text-num" v-if="countdata.start !== 0">{{countdata.start}}</text>
						<view class="item-text">
							进行中
						</view>
					</view>
					<view class="view2-item" @click="gomygame1(3)">
						<image src="../../static/img/my/over.png" style="width: 49rpx;height: 49rpx;" mode=""></image>
						<text class="text-num" v-if="countdata.end !== 0">{{countdata.end}}</text>
						<view class="item-text">
							已结束
						</view>
					</view>
				</view>
			</view>
			<view class="view3" style="background-color: #FFFFFF;border-radius: 18rpx;margin: 0 26rpx;padding: 16rpx 0 16rpx 28rpx;">
				<view class="view2-view" @tap="goJiFen()">
					<image src="../../static/img/my/account.png" class="view2-view-image"></image>
					<view class="view2-view1">
						<view class="view2-view-text">关联游戏账号</view>
						<image src="../../static/img/my/look.png" class="view2-view-image-right"></image>
					</view>
				</view>
				<view class="view2-view" @tap="goWithdraw()">
					<image src="../../static/img/my/money.png" class="view2-view-image" style="height:48rpx;"></image>
					<view class="view2-view1">
						<view class="view2-view-text">我的钱包</view>
						<image src="../../static/img/my/look.png" class="view2-view-image-right"></image>
					</view>
				</view>
				<view class="view2-view" @tap="goSetting()">
					<image src="../../static/img/my/set.png" class="view2-view-image"></image>
					<view class="view2-view1">
						<view class="view2-view-text">系统设置</view>
						<image src="../../static/img/my/look.png" class="view2-view-image-right"></image>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	import tRtPopup from '@/components/views/t-rt-popup/t-rt-popup';
	import tuiButton from '@/components/thorui/tui-button/tui-button.vue';
	import configdata from '../../common/config.js';
	export default {
		components: {
			tuiButton,
			tRtPopup
		},
		data() {
			return {
				userId:'',
				userName:'',
				phone: '',
				countdata:{
					end:0,
					noStart:0,
					start:0
				},
				headImg: '/static/img/my/avatar.png',
			};
		},
		onShow() {
			let headImg = this.$queue.getData('headImg');
			if (headImg && headImg !== 'undefined') {
				this.headImg = headImg;
			} else {
				this.headImg = '/static/img/my/avatar.png';
			}
			this.phone = this.$queue.getData('phone');
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.getUserInfo(userId);
				
			}
			this.countSelect(userId)
		},
		onLoad() {
			let userId = this.$queue.getData('userId');
			if (userId) {
				this.getUserInfo(userId);
			}
		},
		onNavigationBarButtonTap(e) {
			if (e.index === 0) {
				this.$refs.rtBubble.toggle();
			}
		},
		methods: {
			goJiFen(){
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url:'/pages/my/gamenumber'
					})
				} else {
					this.goLoginInfo();
				}
				
			},
			// 个人资料
			statuselect(){
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '/pages/my/userstatus'
					});
				} else {
					this.goLoginInfo();
				}
			},
			uploadImg() {
				let userId = this.$queue.getData('userId');
				if (!userId) {
					this.goLoginInfo();
					return;
				}
				let that = this;
				var url = null;
				uni.showActionSheet({
					// itemList按钮的文字接受的是数组
					itemList: ["查看头像", "从相册选择图片"],
					success(e) {
						var index = e.tapIndex
						if (index === 0) {
							// 用户点击了预览当前图片
							// 可以自己实现当前头像链接的读取
							let url = that.headImg;
							let arr = []
							arr.push(url)
							uni.previewImage({
								// 预览功能图片也必须是数组的
								urls: arr
							})
						} else if (index === 1) {
							uni.chooseImage({
								count: 1, //默认9
								sizeType: ['original', 'compressed'], //可以指定是原图还是压缩图，默认二者都有
								sourceType: ['album'], //从相册选择
								success: function(res) {
									uni.showLoading({
										title: '上传中...'
									});
									let token = uni.getStorageSync('token');
									uni.uploadFile({
										url: that.config("APIHOST1") + '/alioss/upload', //仅为示例，非真实的接口地址
										filePath: res.tempFilePaths[0],
										header: {
											token: token
										},
										name: 'file',
										success: uploadFileRes => {
											url = JSON.parse(uploadFileRes.data);
											that.$Request.postJson('/user/update',{
												userId: userId,
												headImg:url.data
											}).then(res => {
												uni.hideLoading();
												if (res.status === 0) {
													that.$queue.showToast("更新成功");
													that.getUserInfo(userId);
												}
											});
										}
									});
								}
							});
						}
					}
				})
			},
			config: function(name) {
				var info = null;
				if (name) {
					var name2 = name.split("."); //字符分割
					if (name2.length > 1) {
						info = configdata[name2[0]][name2[1]] || null;
					} else {
						info = configdata[name] || null;
					}
					if (info == null) {
						let web_config = cache.get("web_config");
						if (web_config) {
							if (name2.length > 1) {
								info = web_config[name2[0]][name2[1]] || null;
							} else {
								info = web_config[name] || null;
							}
						}
					}
				}
				return info;
			},
			/**
			 * 复制链接
			 */
			copyHref(name) {
				uni.setClipboardData({
					data: name,
					success: r => {
						this.$queue.showToast('复制成功');
					}
				});
			},
			goLogin() {
				let userId = this.$queue.getData('userId');
				if (!userId) {
					this.goLoginInfo();
				}
			},
			gomygame(){
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '../my/game'
					});
				} else {
					this.goLoginInfo();
				}
			},
			gomygame1(eventStatus){
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '../my/game?eventStatus=' + eventStatus
					});
				} else {
					this.goLoginInfo();
				}
			},
			goWithdraw() {
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '../my/wallet'
					});
				} else {
					this.goLoginInfo();
				}
				
			},
			goSetting() {
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '../member/account'
					});	
				} else {
					this.goLoginInfo();
				}
				
			},
			// 获取我的比赛结果数
			countSelect(userId){
				this.$Request.getT('/gameApply/gameCountByUserId?userId=' + userId).then(res => {
					if (res.status === 0) {
						this.countdata = res.data
					} 
				});
			},
			goCustomer() {
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '../member/customer'
					});
				} else {
					this.goLoginInfo();
				}
				
			},
			rtBubble() {
				let userId = this.$queue.getData('userId');
				if (userId) {
					this.$refs.rtBubble.toggle();
				} else {
					this.goLoginInfo();
				}
			},
			itemClick: function(e) {
				let text = ['new', 'myList', 'examine', 'rights'][e.index];
				uni.navigateTo({
					url: `/pages/task/${text}`
				});
			},
			invitati() {
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '../invitation/invitationUser'
					})
				} else {
					this.goLoginInfo();
				}
				
			},
			//获取用户信息
			getUserInfo(userId) {
				this.$Request.getT('/user/find?id=' + userId).then(res => {
					if (res.status === 0) {
						this.$queue.setData('headImg', res.data.headImg);
						this.$queue.setData('userId', res.data.userId);
						this.$queue.setData('userName', res.data.userName);
						this.$queue.setData('phone', res.data.phone);
						this.phone = res.data.phone;
						this.headImg = res.data.headImg;
						this.userName = res.data.userName;
						if (this.userName == null) {
							this.userName = res.data.nickName;
						} else {
							this.userName = res.data.userName;
						}
					} else {
						this.$queue.logout();
					}
				});
			},
			//统一登录跳转
			goLoginInfo() {
				uni.navigateTo({
					url: '/pages/public/loginphone'
				});
			}
		}
	};
</script>

<style>
	
	page{
		background: #ededed;
	}
	
	.content .view1{
		width: 100%;
		height: 450rpx;
		background: url(../../static/img/index/indexbg.png);
		background-size: 100% 100%;
	}
	.order-view {
		display: flex;
		flex-direction: row;
		background-color: #ffffff;
		height: 160upx;
		margin-top: 40upx;
		border-top-left-radius: 40upx;
		border-top-right-radius: 40upx;
		padding-left: 30upx;
	}

	.order-view-image {
		width: 50upx;
		height: 50upx;
	}

	.order-view-view {
		text-align: center;
		padding: 40upx 30upx 40upx 10upx;
		width: 180upx;
	}

	.order-view-text {
		color: #000000;
		font-size: 12px;
		margin-top: 10upx;
		margin-left: 8upx;
	}

	.view2 {
		background-color: #FFFFFF;
		margin: 0 26rpx 40rpx 26rpx;
		border-radius: 16rpx;
		padding: 32rpx 24rpx 34rpx 24rpx;
		color: #000000;
	}
	.view2 .view2-title{
		width: 100%;
		display: flex;
		align-items: center;
		padding-bottom: 28rpx;
		font-size: 32rpx;
		border-bottom: 2rpx solid #ececec;
	}
	.view2 .view2-main{
		display: flex;
		align-items: center;
		margin-top: 29rpx;
		 justify-content:space-between;
	}
	.view2 .view2-main .view2-item{
		text-align: center;
		font-size: 26rpx;
		position: relative;
	}
	
	.view2-item .item-text{
		font-size: 26rpx;
		margin-top: 4rpx;
	}
	.view2-item .text-num{
		position: absolute;
		top: -10rpx;
		left: 44rpx;
		width: 36rpx;
		color: #ffffff;
		border-radius: 50%;
		padding: 2rpx 0;
		background:#ff9e3c ;
	}
	.login-view {
		display: flex;
		flex-direction: row;
		/* #ifdef H5 */
		padding-top: 120upx;
		/* #endif */
		/* #ifndef H5 */
		padding-top: 150upx;
		/* #endif */
	}

	.view2-view {
		display: flex;
		width: 100%;
		align-items: flex-end;
		padding: 20rpx 28rpx 30rpx 0rpx;
		border-bottom: 2rpx solid #ececec;
		margin-bottom: 20rpx;
		padding-right: 28rpx;
	}
	.view3 .view2-view:last-child{
		border: none;
		margin: 0;
	}
	.view2-view1 {
		display: flex;
		flex-direction: row;
		width: 90%;
		align-items: center;
	}

	.view2-view-image {
		width: 56rpx;
		height: 54rpx;
	}

	.view2-view-text {
		font-size: 15px;
		color: #000000;
		margin-left:30upx;
		flex: 1;
	}

	.view2-view-image-right {
		width: 12upx;
		height: 22upx;
		position: relative;
		top: -6rpx;
	}
</style>
