<script>
	import Vue from 'vue';
	export default {
		methods: {
			async getClipboardData() {
				
			}
		},
		onLaunch: function() {
			let that = this;
			//获取全局邀请码
			that.$Request.getT('/common/type/88').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.$queue.setData('publicRelation', res.data.value);
					}
				}
			});
			//获取淘宝APPID
			that.$Request.getT('/common/type/6').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.$queue.setData('taobaoAppid', res.data.value);
					}
				}
			});

			//获取淘宝秘钥
			that.$Request.getT('/common/type/7').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.$queue.setData('taobaoSecret', res.data.value);
					}
				}
			});

			//获取淘宝pid
			that.$Request.getT('/common/type/9').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.$queue.setData('taobaoPid', res.data.value);
					}
				}
			});
			//获取好单库key
			that.$Request.getT('/common/type/10').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.$queue.setData('haodankuKey', res.data.value);
					}
				}
			});
			//获取淘宝名字
			that.$Request.getT('/common/type/11').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.$queue.setData('taobaoName', res.data.value);
					}
				}
			});

			//获取APP下载地址
			that.$Request.getT('/common/type/25').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.$queue.setData('appurl', res.data.value);
					}
				}
			});
			//关键字过滤
			that.$Request.getT('/common/type/67').then(res => {
				if (res.code == 0) {
					if (res.data && res.data.value) {
						this.$queue.setData('searchKeys', res.data.value);
					}
				}
			});
			//#ifdef H5
			let width = window.screen.width;
			if (window.location.href.indexOf('?invitation=') !== -1 || window.location.href.indexOf('&invitation=') !== -1) {
				if (window.location.href.indexOf('?invitation=') !== -1) {
					this.$queue.setData('relation', window.location.href.split('?invitation=')[1].split('&')[0]);
				} else {
					this.$queue.setData('relation', window.location.href.split('&invitation=')[1].split('&')[0]);
				}
			}
			//#endif
			//#ifdef H5
			let relation = this.$queue.getInvitation();
			if (window.location.href.indexOf('?invitation=') !== -1 || window.location.href.indexOf('&invitation=') !== -1) {
				if (window.location.href.indexOf('?invitation=') !== -1) {
					relation = window.location.href.split('?invitation=')[1].split('&')[0];
					this.$queue.setData('relation', window.location.href.split('?invitation=')[1].split('&')[0]);
				} else {
					relation = window.location.href.split('&invitation=')[1].split('&')[0];
					this.$queue.setData('relation', window.location.href.split('&invitation=')[1].split('&')[0]);
				}
			}
			let ua = navigator.userAgent.toLowerCase();
			if (ua.indexOf('micromessenger') !== -1) {
				let openid = this.$queue.getData('openid');
				let userId = this.$queue.getData('userId');
				let that = this;
				if (!openid) {
					if (window.location.href.indexOf('?code=') !== -1 || window.location.href.indexOf('&code=') !== -1) {
						let code;
						if (window.location.href.indexOf('?code=') !== -1) {
							code = window.location.href.split('?code=')[1].split('&')[0];
						} else {
							code = window.location.href.split('&code=')[1].split('&')[0];
						}
						that.$Request.getT('/wx/openId/' + code + '/0').then(res => {
							that.$queue.setData('openid', res.data);
							this.$Request.getT('/userInfo/openid/' + res.data + '/66666666/' + res.msg).then(res => {
								if (res.status === 0) {
									this.$queue.setData('token', res.data.uuid);
									this.$queue.setData('userId', res.data.userId);
									this.$queue.setData('phone', res.data.phone);
									this.$Request.getT(`/user/find?id=${res.data.userId}`).then(res => {
										if (res.status === 0) {
											this.$queue.setData('openid', res.data.openId);
											this.$queue.setData("headImg", res.data.headImg ? res.data.headImg : '/static/img/common/logo.jpg');
											this.$queue.setData('userId', res.data.userId);
											this.$queue.setData('userName', res.data.userName);
											if(res.data.userName==null || res.data.userName== ''){
												this.$queue.setData('userName', res.data.nickName);
											}
										}
									});
								}
							});
						});
					} else {
						window.location.href =
							'https://open.weixin.qq.com/connect/oauth2/authorize?appid=' +
							that.$queue.getWxAppid() +
							'&redirect_uri=' +
							window.location.href.split('#')[0] +
							'&response_type=code&scope=snsapi_userinfo#wechat_redirect';
					}
				} else {
					this.$Request.getT('/userInfo/openid/' + openid + '/66666666/').then(res => {
						if (res.status === 0) {
							this.$queue.setData('token', res.data.uuid);
							this.$queue.setData('userId', res.data.userId);
							this.$queue.setData('openid', res.data.openId);
							this.$queue.setData('phone', res.data.phone);
							this.$Request.getT(`/user/find?id=${res.data.userId}`).then(res => {
								if (res.status === 0) {
									this.$queue.setData('openid', res.data.openId);
									this.$queue.setData("headImg", res.data.headImg ? res.data.headImg : '/static/img/common/logo.jpg');
									this.$queue.setData('userId', res.data.userId);
									this.$queue.setData('userName', res.data.userName);
									if(res.data.userName==null || res.data.userName== ''){
										this.$queue.setData('userName', res.data.nickName);
									}
									this.$queue.setData('phone', res.data.phone);
								}
							});
						}
					});
				}
			}
			//#endif
		},

		onShow: function() {
			// #ifdef APP-PLUS
			this.getClipboardData();
			if (uni.getSystemInfoSync().platform == 'android') {
				let clientid = plus.push.getClientInfo().clientid;
				// console.error(clientid);
				let userId = this.$queue.getData('userId');
				if (userId) {
					this.$Request.postT('/appLogin/updateClientId?clientId=' + clientid + '&userId=' + userId).then(res => {});
				}
			}

			//#endif
			let userId = this.$queue.getData('userId');
			let that = this;
			if (userId) {
				//获取是否开启自动签到
				that.$Request.getT('/common/type/111').then(res => {
					if (res.code == 0) {
						if (res.data && res.data.value) {
							if (res.data.value === '是') {
								that.$Request.getT('/integral/signIn?userId=' + userId).then(res => {
									if (res.code === 0) {
										console.error('签到成功')
									}
								});
							}
						}
					}
				});
			}

		},
		onHide: function() {}
	};
</script>

<style lang="scss">
	/*每个页面公共css */
	@import 'colorui/main.css';
	@import 'colorui/icon.css';
	@import './static/css/common.css';
	@import './static/css/simplepro.css';

	/*
            全局公共样式和字体图标
        */
	@font-face {
		font-family: yticon;
		font-weight: normal;
		font-style: normal;
		src: url('https://at.alicdn.com/t/font_1078604_w4kpxh0rafi.ttf') format('truetype');
	}

	.yticon {
		font-family: 'yticon' !important;
		font-size: 16px;
		font-style: normal;
		-webkit-font-smoothing: antialiased;
		-moz-osx-font-smoothing: grayscale;
	}

	.nav-li text {
		position: absolute;
		right: 30 upx;
		top: 30 upx;
		font-size: 52 upx;
		width: 60 upx;
		height: 60 upx;
		text-align: center;
		line-height: 60 upx;
	}

	uni-tabbar .uni-tabbar__item .uni-tabbar__icon {
		width: 46rpx;
		height: 42rpx!important;
	}

	@keyframes show {
		0% {
			transform: translateY(-50px);
		}

		60% {
			transform: translateY(40 upx);
		}

		100% {
			transform: translateY(0px);
		}
	}

	@-webkit-keyframes show {
		0% {
			transform: translateY(-50px);
		}

		60% {
			transform: translateY(40 upx);
		}

		100% {
			transform: translateY(0px);
		}
	}

	.icon-yiguoqi1:before {
		content: '\e700';
	}

	.icon-iconfontshanchu1:before {
		content: '\e619';
	}

	.icon-iconfontweixin:before {
		content: '\e611';
	}

	.icon-alipay:before {
		content: '\e636';
	}

	.icon-shang:before {
		content: '\e624';
	}

	.icon-shanchu4:before {
		content: '\e622';
	}

	.icon-xiaoxi:before {
		content: '\e618';
	}

	.icon-jiantour-copy:before {
		content: '\e600';
	}

	.icon-fenxiang2:before {
		content: '\e61e';
	}

	.icon-pingjia:before {
		content: '\e67b';
	}

	.icon-daifukuan:before {
		content: '\e68f';
	}

	.icon-pinglun-copy:before {
		content: '\e612';
	}

	.icon-dianhua-copy:before {
		content: '\e621';
	}

	/*.icon-shoucang:before {*/
	/*    content: "\e645";*/
	/*}*/

	.icon-xuanzhong2:before {
		content: '\e62f';
	}

	.icon-gouwuche_:before {
		content: '\e630';
	}

	.icon-icon-test:before {
		content: '\e60c';
	}

	.icon-icon-test1:before {
		content: '\e632';
	}

	.icon-bianji:before {
		content: '\e646';
	}

	.icon-jiazailoading-A:before {
		content: '\e8fc';
	}

	.icon-zuoshang:before {
		content: '\e613';
	}

	.icon-jia2:before {
		content: '\e60a';
	}

	.icon-huifu:before {
		content: '\e68b';
	}

	.icon-arrow-fine-up:before {
		content: '\e601';
	}

	.icon-hot:before {
		content: '\e60e';
	}

	.icon-lishijilu:before {
		content: '\e6b9';
	}

	.icon-zhengxinchaxun-zhifubaoceping-:before {
		content: '\e616';
	}

	.icon-naozhong:before {
		content: '\e64a';
	}

	.icon-xiatubiao--copy:before {
		content: '\e608';
	}

	.icon-shoucang_xuanzhongzhuangtai:before {
		content: '\e6a9';
	}

	.icon-jia1:before {
		content: '\e61c';
	}

	.icon-bangzhu1:before {
		content: '\e63d';
	}

	.icon-arrow-left-bottom:before {
		content: '\e602';
	}

	.icon-arrow-right-bottom:before {
		content: '\e603';
	}

	.icon-arrow-left-top:before {
		content: '\e604';
	}

	.icon-icon--:before {
		content: '\e744';
	}

	.icon-zuojiantou-up:before {
		content: '\e605';
	}

	.icon-xia:before {
		content: '\e62d';
	}

	.icon--jianhao:before {
		content: '\e60b';
	}

	.icon-weixinzhifu:before {
		content: '\e61a';
	}

	.icon-comment:before {
		content: '\e64f';
	}

	.icon-weixin:before {
		content: '\e61f';
	}

	.icon-fenlei1:before {
		content: '\e620';
	}

	.icon-erjiye-yucunkuan:before {
		content: '\e623';
	}

	.icon-Group-:before {
		content: '\e688';
	}

	.icon-you:before {
		content: '\e606';
	}

	.icon-forward:before {
		content: '\e607';
	}

	.icon-tuijian:before {
		content: '\e610';
	}

	.icon-bangzhu:before {
		content: '\e679';
	}

	.icon-share:before {
		content: '\e656';
	}

	.icon-yiguoqi:before {
		content: '\e997';
	}

	.icon-shezhi1:before {
		content: '\e61d';
	}

	.icon-fork:before {
		content: '\e61b';
	}

	.icon-kafei:before {
		content: '\e66a';
	}

	.icon-iLinkapp-:before {
		content: '\e654';
	}

	.icon-saomiao:before {
		content: '\e60d';
	}

	.icon-shezhi:before {
		content: '\e60f';
	}

	.icon-shouhoutuikuan:before {
		content: '\e631';
	}

	.icon-gouwuche:before {
		content: '\e609';
	}

	.icon-dizhi:before {
		content: '\e614';
	}

	.icon-fenlei:before {
		content: '\e706';
	}

	.icon-xingxing:before {
		content: '\e70b';
	}

	.icon-tuandui:before {
		content: '\e633';
	}

	.icon-zuanshi:before {
		content: '\e615';
	}

	.icon-zuo:before {
		content: '\e63c';
	}

	.icon-shoucang2:before {
		content: '\e62e';
	}

	.icon-shouhuodizhi:before {
		content: '\e712';
	}

	.icon-yishouhuo:before {
		content: '\e71a';
	}

	.icon-dianzan-ash:before {
		content: '\e617';
	}
</style>
