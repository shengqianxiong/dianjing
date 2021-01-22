<template>
	<view class="container">
		<view class="wrapper">
			<view class="input-content">
				<view class="cu-form-group" style="border: 2upx solid whitesmoke;margin-bottom: 20px;border-radius: 30px">
					<view class="title">手机号</view>
					<input type="number" :value="phone" placeholder="请输入手机号" maxlength="11" data-key="phone" @input="inputChange" />
				</view>
				<view class="cu-form-group" style="border: 2upx solid whitesmoke;border-radius: 30px">
					<view class="title">密码</view>
					<input type="password" placeholder="请输入密码" maxlength="20" :value="password" data-key="password" @input="inputChange"
					 @confirm="toLogin" />
					<text class="send-msg" @click="forget">忘记密码</text>
				</view>
			</view>
			<button class="confirm-btn" @click="toLogin">登录</button>

			<view style="margin-top: 32px;text-align: center">
				<view>没有账号？
					<text style="color: #ff9e3c" @click="register()">立即注册</text>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				phone: '',
				password: '',
				loginName:'',
				sending: false,
				sendTime: '获取验证码',
				count: 60,
			}
		},
		methods: {
			forget() {
				uni.navigateTo({
					url: '/pages/public/forgetPwd'
				});
			},
			register() {
				uni.navigateTo({
					url: '/pages/public/register'
				});
			},
			inputChange(e) {
				const key = e.currentTarget.dataset.key;
				this[key] = e.detail.value;
			},
			navBack() {
				uni.navigateBack();
			},

			toLogin() {
				this.$queue.loginClear();
				let openid = this.$queue.getData("openid");
				const {
					phone,
					password
				} = this;
				if (!phone) {
					this.$queue.showToast("请输入手机号");
				} else if (phone.length != 11) {
					this.$queue.showToast("请输入正确的手机号");
				} else if (!password) {
					this.$queue.showToast("请输入密码");
				} else {
					this.$queue.showLoading("正在登录中...");
					this.$Request.getT("/user/login", {
						password: password,
						loginName: phone,
					}).then(res => {
						if (res.status === 0) {
							this.$queue.setData("userId", res.data.userId);
							this.$queue.setData("phone", res.data.phone);
							this.$queue.setData("userName", res.data.userName);
							this.$queue.setData("headImg", res.data.headImg);
							uni.hideLoading();
							uni.switchTab({
								url: '/pages/index/index'
							});
						} else {
							uni.hideLoading();
							this.$queue.showToast(res.msg);
						}
					});
				}
			}
		},

	}
</script>

<style lang='scss'>
	.footer {
		padding-left: 140upx;
		margin-top: 32upx;
		font-size: 24upx;
		color: #666666;
		text-align: center;
		display: flex;
	}

	page {
		background: #fff;
	}

	.send-msg {
		border-radius: 30px;
		color: black;
		background: white;
		height: 30px;
		font-size: 14px;
		line-height: 30px;
	}

	.container {
		top: 0;
		padding-top: 32upx;
		position: relative;
		width: 100%;
		height: 100%;
		overflow: hidden;
		background: #fff;
	}

	.wrapper {
		position: relative;
		z-index: 90;
		background: #fff;
		padding-bottom: 20px;
	}

	.back-btn {
		position: absolute;
		left: 20px;
		z-index: 9999;
		padding-top: var(--status-bar-height);
		top: 20px;
		font-size: 20px;
		color: $font-color-dark;
	}

	.left-top-sign {
		font-size: 80px;
		color: $page-color-base;
		position: relative;
	}


	.right-top-sign {
		position: absolute;
		top: 40px;
		right: -15px;
		z-index: 95;

		&:before,
		&:after {
			display: block;
			content: "";
			width: 20px;
			height: 40px;
			background: #e10a07;
		}

		&:before {
			transform: rotate(50deg);
			border-radius: 0 50px 0 0;
		}

		&:after {
			position: absolute;
			right: -198px;
			top: 0;
			transform: rotate(-50deg);
			border-radius: 50px 0 0 0;
			/* background: pink; */
		}
	}

	.left-bottom-sign {
		position: absolute;
		left: -270px;
		bottom: -320px;
		/*border: 100upx solid #d0d1fd;*/
		border-radius: 50%;
		padding: 90px;
	}

	.welcome {
		position: relative;
		left: 30px;
		top: -55px;
		font-size: 28px;
		color: #555;
		text-shadow: 1px 0px 1px rgba(0, 0, 0, .3);
	}

	.input-content {
		padding: 0 20px;
	}

	.input-item {
		display: flex;
		flex-direction: column;
		align-items: flex-start;
		justify-content: center;
		padding: 0 30px;
		background: $page-color-light;
		height: 64px;
		border-radius: 4px;
		margin-bottom: 30px;

		&:last-child {
			margin-bottom: 0;
		}

		.tit {
			height: 30px;
			line-height: 28px;
			font-size: $font-sm+2upx;
			color: $font-color-base;
		}

		input {
			height: 40px;
			font-size: $font-base + 2upx;
			color: $font-color-dark;
			width: 100%;
		}
	}

	.confirm-btn {
		width: 300px;
		height: 42px;
		line-height: 42px;
		border-radius: 30px;
		margin-top: 40px;
		background: #ff9e3c;
		/* background: -moz-linear-gradient(left, #F15B6C, #e10a07 100%);
		background: -webkit-gradient(linear, left top, left right, color-stop(0, #F15B6C), color-stop(100%, #e10a07));
		background: -webkit-linear-gradient(left, #F15B6C 0, #e10a07 100%);
		background: -o-linear-gradient(left, #F15B6C 0, #e10a07 100%);
		background: -ms-linear-gradient(left, #F15B6C 0, #e10a07 100%);
		background: linear-gradient(to left, #F15B6C 0, #e10a07 100%); */
		color: #fff;
		font-size: $font-lg;

		&:after {
			border-radius: 60px;
		}
	}

	.confirm-btn1 {
		width: 300px;
		height: 42px;
		line-height: 42px;
		border-radius: 30px;
		margin-top: 40px;
		background: whitesmoke;
		color: grey;
		font-size: $font-lg;

		&:after {
			border-radius: 60px;
		}
	}

	.forget-section {
		font-size: $font-sm+2upx;
		color: $font-color-spec;
		text-align: center;
		margin-top: 40px;
	}

	.register-section {
		left: 0;
		margin-top: 30px;
		bottom: 30px;
		width: 100%;
		font-size: $font-sm+2upx;
		color: $font-color-base;
		text-align: center;

		text {
			color: $font-color-spec;
			margin-left: 10px;
		}
	}
</style>
