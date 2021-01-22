<template>
    <view class="container">
		<!-- #ifdef APP-PLUS -->
		<view class="list-cell b-b" @click="navTo('/pages/public/pwd')" hover-class="cell-hover"
		      :hover-stay-time="50">
		    <text class="cell-tit">修改密码</text>
		    <text class="cell-more yticon icon-you"></text>
		</view>
		<!-- #endif -->
        <view class="list-cell b-b" style="margin-top: 2upx" @click="cacheTo"
              hover-class="cell-hover"
              :hover-stay-time="50">
            <text class="cell-tit">清除缓存</text>
            <text class="cell-more yticon icon-you"></text>
        </view>
		<view class="list-cell b-b" style="margin-top: 2upx" @click="toAgreement"
		      hover-class="cell-hover"
		      :hover-stay-time="50">
		    <text class="cell-tit">用户协议</text>
		    <text class="cell-more yticon icon-you"></text>
		</view>
		<!-- #ifdef APP-PLUS -->
		<view @click="toLogout">
			<button class="setting-btn">退出登录</button>
		</view>
		<!-- #endif -->
    </view>
</template>

<script>
    import {
        mapMutations
    } from 'vuex';

    export default {
        data() {
            return {
                isShow: false,
                version: '',
                userId:'',
				headImg:'',
				userName:''
            };
        },
        onShow() {
            let userId = this.$queue.getData("userId");
            if (userId) {
                this.isShow = true;
            }
        },
        methods: {
			// 用户协议
			toAgreement(){
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '../member/xieyi'
					});
				} else {
					uni.navigateTo({
					    url: '/pages/public/loginphone'
					});
				}
			},
			//退出登录
			toLogout() {
				let that = this;
				uni.showModal({
					title: '退出提醒',
					content: '确定要退出登录么',
					success: e => {
						if (e.confirm) {
							that.$queue.remove('userId');
							that.$queue.remove('phone');
							that.$queue.remove('userName');
							that.isShow = false;
							setTimeout(() => {
								uni.showToast({
									icon: 'none',
									title: '登录已退出'
								});
								uni.navigateBack();
							}, 200);
						}
					}
				});
			},
            ...mapMutations(['logout']),

            navTo(url) {
                let userId = this.$queue.getData("userId");
                if (userId) {
                    uni.navigateTo({
                        url: url
                    });
                } else {
                    uni.navigateTo({
                        url: '/pages/public/loginphone'
                    });
                }
            },
			cacheTo() {
				let that = this;
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.showModal({
						title: '清除缓存',
						content: '确定要清除缓存么',
						success: e => {
							if (e.confirm) {
								// that.$queue.remove('userId');
								// that.$queue.remove('phone');
								// that.$queue.remove('userName');
								that.isShow = false;
								setTimeout(() => {
									uni.showToast({
										icon: 'none',
										title: '清除缓存成功'
									});
								}, 200);
							}
						}
					});
				} 
			},
            navTo1(url) {
                uni.navigateTo({
                    url: url
                });
            },
            about(url) {
                uni.showModal({
                    showCancel: false,
                    title: '省钱兄',
                    content: '省钱兄是一个汇聚了全网隐藏优惠券的网购返利软件 ！这里汇聚淘宝、京东、天猫、拼多多等商城优惠券信息！每天实时更新优惠券，发放一些商家的大额优惠券！这里可以帮你网购省钱返利，也可以帮你兼职赚钱！每年最高能让你网购省50%的钱！\n\n西安省钱兄网络科技有限公司',
                    success: (e) => {
                        if (e.confirm) {

                        }
                    }
                });
            },
			//获取用户信息
			getUserInfo(userId) {
				this.$Request.getT('/user/find?id=' + userId).then(res => {
					if (res.status === 0) {
						this.$queue.setData('headImg', res.data.headImg);
						this.$queue.setData('userId', res.data.userId);
						this.$queue.setData('userName', res.data.userName);
						this.$queue.setData('phone', res.data.phone);
						if (res.data.headImg) {
							this.headImg = res.data.headImg;
						} else {
							this.headImg = '/static/img/common/logo.jpg';
						}
						if (res.data.userName) {
							this.phone = res.data.userName;
						} else {
							this.phone = res.data.phone;
						}
					} else {
						this.$queue.logout();
					}
				});
			},
            //switch
            switchChange(e) {
                let statusTip = e.detail.value ? '打开' : '关闭';
                this.$api.msg(`${statusTip}消息推送`);
            },
            update() {
                //#ifdef APP-PLUS
                // APP检测更新 具体可以参考：https://ask.dcloud.net.cn/article/35667
                plus.runtime.getProperty(plus.runtime.appid, widgetInfo => {
                    this.$Request.get("/update.json").then(res => {
                        console.error(res.wgtUrl);
                        console.error(res.version);
                        console.error(widgetInfo.version);
                        if (res.wgtUrl && widgetInfo.version < res.version) {
                            uni.downloadFile({
                                url: res.wgtUrl,
                                success: (downloadResult) => {
                                    if (downloadResult.statusCode === 200) {
                                        plus.runtime.install(downloadResult.tempFilePath, {
                                            force: false
                                        }, (d) => {
                                            console.log('install success...');
                                            plus.runtime.restart();
                                        }, (e) => {
                                            console.error('install fail...');
                                        })
                                    }
                                }
                            })
                        }
                    })
                });
                //#endif
            }

        }
    }
</script>

<style lang='scss'>
    page {
        background: $page-color-base;
    }
	.setting-btn{
		width: 455rpx;
		height: 96rpx;
		line-height: 96rpx;
		border:none;
		color: #ffffff;
		font-size: 28rpx;
		margin-top: 150rpx;
		border-radius:40rpx;
		background-color: #ff9e3c;
	}
    .list-cell {
        display: flex;
        align-items: baseline;
        padding: 20upx $page-row-spacing;
        line-height: 60upx;
        position: relative;
        background: #fff;
        justify-content: center;

        &.log-out-btn {
            margin-top: 40upx;

            .cell-tit {
                color: $uni-color-primary;
                text-align: center;
                margin-right: 0;
            }
        }

        &.cell-hover {
            background: #fafafa;
        }

        &.b-b:after {
            left: 32upx;
        }

        &.m-t {
            margin-top: 18upx;
        }

        .cell-more {
            align-self: baseline;
            font-size: $font-lg;
            color: $font-color-light;
            margin-left: 12upx;
        }

        .cell-tit {
            flex: 1;
            font-size: $font-base + 2upx;
            color: $font-color-dark;
            margin-right: 12upx;
        }

        .cell-tip {
            font-size: $font-base;
            color: $font-color-light;
        }

        switch {
            transform: translateX(8px) scale(.84);
        }
    }
</style>
