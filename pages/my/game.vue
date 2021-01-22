<template>
	<view class="game-main">
		<view class="game-content">
			<view class="game-item" v-for="(item,index) in navlist" :key="index" :class="{ 'active': tabIndex == item.value }"
			 @tap="tabClick(item)">
				{{item.label}}
			</view>
		</view>
		<view class="game-list">
			<view class="list-item" @click="gamedetails(item)" v-for="(item,index) in matchdata" :key="index" style="background-color: #FFFFFF;margin-top:20rpx; color: #333333;padding: 20rpx;">
				<view class="gamelist-item">
					<view>
						游戏名称:<text>{{item.gameTypeName}}</text>
					</view>
					<view class="item-right">
						区域:<text v-if="item.area==1">微信</text><text v-if="item.area==2">QQ</text>
					</view>
				</view>
				<view class="gamelist-item">
					<view>赛事名称:<text>{{item.title}}</text></view>
					<view class="item-right">
						标签:{{item.tag}}
					</view>
				</view>
				<view class="gamelist-item" v-if="item.gameEvent">
					<view v-if="item.eventStatus==1">开始时间:{{item.gameEvent.startTime}} </view>
				</view>
				<view class="gamelist-item" style="display: block;" v-if="item.eventStatus==2">
					<view v-if="item.eventStatus==2 && item.gameEvent.roomUrl">房间链接:{{item.gameEvent.roomUrl}} <text style="float: right;background-color: #f2ad44;color: #ffffff;font-size: 26rpx;padding: 6rpx 40rpx;border-radius: 6rpx;" @click="copy(item.gameEvent.roomUrl)">复制</text> </view>
					<view v-if="item.eventStatus==2 && item.gameEvent.roomNumber" style="margin: 20rpx 0;">房间号:{{item.gameEvent.roomNumber}}  <text style="float: right;background-color: #f2ad44;color: #ffffff;font-size: 26rpx;padding: 6rpx 40rpx;border-radius: 6rpx;" @click="copy(item.gameEvent.roomNumber)">复制</text> </view>
					<view v-if="item.eventStatus==2 && item.gameEvent.roomImg" style="display: flex;align-items: center;">
						<text style="margin-right: 20rpx;">房间二维码: </text>
						<view style="flex: 1;text-align: right;" @click="uploadImg1()">
							<image :src="item.gameEvent.roomImg" style="width: 140rpx; height:140rpx;" ></image>
						</view>
					</view>
				</view>
				<view class="gamelist-item" v-if="item.eventStatus==3">
					<view v-if="item.eventStatus==3">结束时间:{{item.gameEvent.endTime}}
					</view>
					<view class="item-right" v-if="item.eventStatus==3 && item.settleStatus !==2">
						<text @click="account(item)" style="background-color: #f2ad44;color: #ffffff;font-size: 26rpx;padding: 10rpx 40rpx;border-radius: 6rpx;">结算</text>
					</view>
					<view class="item-right" v-if="item.eventStatus==3 && item.settleStatus ==2">
						<text @click="particulars(item)" style="background-color: #f2ad44;color: #ffffff;font-size: 26rpx;padding: 10rpx 40rpx;border-radius: 6rpx;">记录</text>
					</view>
				</view>
			</view>
			<!-- 加载更多提示 -->
			<view class="s-col is-col-24" v-if="matchdata.length > 0">
				<load-more :loadingType="loadingType" :contentText="contentText"></load-more>
			</view>
			<!-- 加载更多提示 -->
			<empty v-if="matchdata.length === 0" des="暂无数据" show="false"></empty>
		</view>
		<!-- 结算 -->
		<uni-popup ref="popup" type="center">
			<view style="background-color: #ffffff;width:600rpx; height:620rpx;border-radius: 12rpx;padding: 20rpx;">
				<view>
					<span>上传比赛结果图</span>
					<view class="screenshot" style="text-align: center;margin-top:20rpx;" @click="uploadImg()">
						<image v-if="uploadimg==''" src="../../static/img/my/uploading.png" style="width: 360rpx;height:360rpx;" mode=""></image>
						<image v-else :src="uploadimg" style="width: 360rpx;height:360rpx;"></image>
					</view>
				</view>
				<view class="footer-btn2">
					<view class="gamebind-btn" @click="goaccount">
						确定
					</view>
				</view>
			</view>
		</uni-popup>
		<!-- 记录 -->
		<uni-popup ref="popup1" type="center">
			<view style="background-color: #ffffff;padding:20rpx;height:420rpx; border-radius: 12rpx;">
				<view style="text-align: center;font-size: 32rpx;color: #000000;margin-bottom: 40rpx;">结算记录</view> 
				
				<view class="settle-item">
					结算内容:{{settle.content}}
				</view>
				<view class="settle-item">
					结算明细:{{settle.title}}
				</view>
				<view class="settle-item">
					结算金额:{{settle.money}}
				</view>
				<view class="settle-item">
					结算时间:{{settle.createTime}}
				</view>
			</view>
		</uni-popup>
	</view>
</template>

<script>
	import uniPopup from '@/components/uni-popup/uni-popup.vue'
	import uniPopupMessage from '@/components/uni-popup/uni-popup-message.vue'
	import uniPopupDialog from '@/components/uni-popup/uni-popup-dialog.vue'
	import configdata from '../../common/config.js';
	export default {
		data() {
			return {
				page: 1,
				size: 5,
				userId: '',
				eventStatus: 1,
				tabIndex: 1,
				uploadimg: '',
				settle:{},
				matchdata: [],
				accountid: '',
				navlist: [{
						label: '待开始',
						value: 1
					},
					{
						label: '进行中',
						value: 2
					},
					{
						label: '已结束',
						value: 3
					}
				],
				loadingType: 0,
				scrollTop: false,
				contentText: {
					contentdown: '上拉显示更多',
					contentrefresh: '正在加载...',
					contentnomore: '没有更多数据了'
				}
			};
		},
		components: {
			uniPopup,
			uniPopupMessage,
			uniPopupDialog
		},
		onLoad(e) {
			if (JSON.stringify(e) === '{}') {
				this.eventStatus = 1
				this.mymatch(this.eventStatus)
			} else {
				this.eventStatus = e.eventStatus
				this.tabIndex = e.eventStatus
				this.mymatch(this.eventStatus)
			}

		},
		methods: {
			tabClick(tab) {
				this.tabIndex = tab.value
				this.matchdata = [];
				this.mymatch(this.tabIndex)
			},
			// 获取我的比赛记录
			mymatch(eventStatus) {
				this.loadingType = 1;
				uni.showLoading({
					title: '加载中...'
				});
				let userId = this.$queue.getData("userId");
				this.$Request.getT(`/gameApply/userList?page=${this.page - 1}&size=${this.size}&userId=${userId}&eventStatus=${eventStatus}`).then(
					res => {
						if (res.status === 0) {
							if (this.page === 0) {
								this.matchdata = [];
							}
							res.data.content.forEach(d => {
								this.matchdata.push(d);
							});
							if (res.data.content.length === this.size) {
								this.loadingType = 0
							} else {
								this.loadingType = 3
							}
							uni.hideLoading()
						} else {
							this.loadingType = 2;
							uni.hideLoading();
						}
					});
			},
			// 复制
			copy(value){
			  //提示模板
			  uni.showModal({
			    content:value,//模板中提示的内容
			    confirmText:"复制内容",
			    success:(e)=>{//点击复制内容的后调函数
				if (e.confirm) {
					//uni.setClipboardData方法就是讲内容复制到粘贴板
					uni.setClipboardData({
					  data:value,//要被复制的内容
					  success:()=>{//复制成功的回调函数
					    uni.showToast({//提示
					      title:"复制成功"
					    })
					  }
					});
				}
			    }
			  });
			},
			// 跳转详情
			gamedetails(item){
				uni.navigateTo({
					url: '../index/match?id=' + item.gameEventId +'&userId=' + item.userId
				});
			},
			// 结算弹框
			account(item) {
				if (item.settleStatus !== 2) {
					if(item.settleImg ==''){
						this.uploadimg = item.settleImg
					}
					this.$refs.popup.open()
					this.accountid = item.id
				} else {
					this.$queue.showToast('赛事已结算');
				}
			},
			goaccount() {
				if(this.uploadimg==''){
					this.$queue.showToast('请上传比赛结果图');
				}else{
					this.$Request.getT(`/gameApply/settle?id=${this.accountid}&settleImg=${this.uploadimg}`).then(res => {
						if (res.status === 0) {
							this.$queue.showToast('操作成功，等待结果');
							this.$refs.popup.close()
						}
					});
				}
			},
			particulars(item) {
				if(item.userMoneyDetailsId == null && item.settleStatus == 2){
					this.$queue.showToast('结算为0，无记录');
				}else{
					this.$Request.getT(`/userMoney/find?id=${item.userMoneyDetailsId}`).then(res => {
						if (res.status === 0) {
							this.$refs.popup1.open()
							this.settle = res.data
						}
					});
				}
			},
			uploadImg1() {
				let that = this;
				var url = null;
				uni.showActionSheet({
					// itemList按钮的文字接受的是数组
					itemList: ["查看图片"],
					success(e) {
						var index = e.tapIndex
						// 用户点击了预览当前图片
						// 可以自己实现当前头像链接的读取
						let url = that.matchdata[index].gameEvent.roomImg;
						let arr = []
						arr.push(url)
						uni.previewImage({
							// 预览功能图片也必须是数组的
							urls: arr
						})
					}
				})
			},
			uploadImg() {
				let that = this;
				var url = null;
				uni.showActionSheet({
					// itemList按钮的文字接受的是数组
					itemList: ["从相册选择图片"],
					success(e) {
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
										uni.hideLoading();
										url = JSON.parse(uploadFileRes.data);
										that.uploadimg = url.data
									}
								});
							}
						});
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
			onReachBottom: function() {
				this.page = this.page + 1;
				this.mymatch(this.tabIndex);
			}
		}
	};
</script>

<style>
	.game-content {
		display: flex;
		background-color: #FFFFFF;
		color: #333333;
	}
	.settle-item{
		margin: 20rpx;
	}
	.game-content .game-item {
		flex: 1;
		text-align: center;
		padding: 30rpx 0;
	}

	.game-content .game-item.active {
		color: #ffa03b;
		border-bottom: 4rpx solid #ffa03b;
	}

	.gamelist-item {
		display: flex;
		padding: 10rpx 30rpx;
		font-size: 30rpx;
	}

	.item-right {
		text-align: right;
		flex: 1;
	}

	.footer-btn2 {
		margin-top: 20rpx;
	}

	.footer-btn2 .gamebind-btn {
		margin: 0 60rpx;
		padding: 20rpx 0;
		text-align: center;
		color: #FFFFFF;
		font-size: 30rpx;
		border-radius: 40rpx;
		background: #ff9e3c;
	}
</style>
