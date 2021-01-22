<template>
	<view>
		<view class="usermain">
			<view class="usermain-item">
				<view>头像</view>
				<view @click="uploadImg()">
					<image src="../../static/img/my/avatar.png" v-if="headImg==null" mode="" style="width: 111rpx;height: 111rpx;border-radius: 50%;"></image>
					<image v-else :src="headImg" mode="" style="width: 111rpx;height: 111rpx;border-radius: 50%;"></image>
				</view>
			</view>
			<view class="usermain-item item-padding">
				<view>用户名</view>
				<view>
					<view class="cu-form-group">
						<input  v-model="userName" placeholder="请输入用户名" />
					</view>
				</view>
			</view>
			<view class="usermain-item item-padding" v-if="platform=='weixin'">
				<view>微信名</view>
				<view>
					<view class="cu-form-group">
						<input  v-model="nickName" placeholder="请输入微信名" disabled />
					</view>
				</view>
			</view>
			<view class="usermain-item item-padding">
				<view>姓名</view>
				<view class="cu-form-group">
					<input  v-model="realName" placeholder="请填写您的真实姓名" />
				</view>
			</view>
			<view class="usermain-item item-padding">
				<view>手机</view>
				<view>
					<view class="cu-form-group" @click="userphone">
						<text>{{phone}}</text> <image src="../../static/img/my/return.png" style="width: 12rpx; height: 19rpx;margin-left: 10rpx;" mode=""></image>
					</view>
				</view>
			</view>
		</view>
		<view class="footer-btn">
			<view class="usermain-btn" @click="messagebtn()">保存</view>
		</view>
	</view>
</template>

<script>
	import configdata from '../../common/config.js';
	export default {
		data() {
			return {
				phone: '',
				headImg:'',
				userName:'',
				nickName:'',
				userId:'',
				realName:'',
				password:'',
				platform:'',
				createTime:'',
				money :'',
				jiFen :'',
				status :'',
				zhiFuBao :'',
				zhiFuBaoName :''
			};
		},
		onLoad(e) {
			this.getUserInfo()
		},
		methods: {
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
			getUserInfo() {
				let userId = this.$queue.getData('userId');
				this.$Request.getT('/user/find?id=' + userId).then(res => {
					if (res.status === 0) {
						this.userName = res.data.userName;
						this.phone = res.data.phone;
						this.headImg = res.data.headImg;
						this.realName = res.data.realName;
						this.nickName = res.data.nickName;
						this.userId = res.data.userId;
						this.password = res.data.password;
						this.createTime = res.data.createTime;
						this.money = res.data.money;
						this.platform = res.data.platform;
						console.log(this.platform)
						this.jiFen = res.data.jiFen;
						this.status = res.data.status;
						this.zhiFuBao = res.data.zhiFuBao;
						this.zhiFuBaoName = res.data.zhiFuBaoName;
					} else {
						uni.showModal({
							showCancel: false,
							title: '未知错误',
							content: res.msg
						});
						this.$queue.logout();
					}
					uni.hideLoading();
				});
			},
			// 保存
			messagebtn(){
				if(this.userName == ''){
					this.$queue.showToast('用户名不能为空');
				}else{
					uni.showModal({
						title: '提示',
						content: '确定保存信息',
						success: e => {
							if (e.confirm) {
								let userId = this.$queue.getData("userId");
								this.$Request.postJson("/user/update", {
									userId: userId,
									area:this.area,
									userName:this.userName,
									headImg:this.headImg,
									realName:this.realName,
									phone:this.phone,
									password:this.password,
									createTime:this.createTime,
									money:this.money,
									jiFen:this.jiFen,
									zhiFuBao:this.zhiFuBao,
									zhiFuBaoName:this.zhiFuBaoName,
								}).then(res => {
									if (res.status === 0) {
										this.$queue.showToast('保存成功');
									} else {
										this.$queue.showToast(res.msg);
									}
								});
							}
						}
					});
				}
			},
			userphone(){
				uni.navigateTo({
					url:'/pages/my/userphone'
				})
			}
		}
	};
</script>

<style>
	page{
		background: #EDEDED;
	}
	.usermain{
		background: #FFFFFF;
	}
	.usermain-item{
		display: flex;
		align-items: center;
		margin-left: 40rpx;
		padding:30rpx 40rpx 30rpx 0;
		justify-content: space-between;
		border-bottom: 2rpx solid #f2f2f2;
	}
	.usermain-item.item-padding{
		padding:0 40rpx 0 0;
	}
	.cu-form-group{
		padding: 0;
		text-align: right;
	}
	.cu-form-group input{
		font-size: 28rpx;
		color: #333333;
		
	}
	.footer-btn{margin-top: 150rpx;}
	.footer-btn .usermain-btn{
		color: #ffffff;
		background: #ff9e3c;
		text-align: center;
		width: 450rpx;
		height: 96rpx;
		font-size: 28rpx;
		line-height: 96rpx;
		margin: 0 auto;
		border-radius: 40rpx;
	}
</style>
