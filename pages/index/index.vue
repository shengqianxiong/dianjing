<template>
	<view class="index-content">
			<view class="index_bg"> 
				<image src="../../static/img/index/index-bgimg.png"></image>
			</view>
			<view class="swiper-mian">
				<view class="swiper swiper-fiexd">
						<swiper class="swiper-container" :autoplay="true" :interval="4000" :circular="true" :indicator-dots="true"
						 indicator-active-color="#e10a07" indicator-color="#cccccc">
							<block v-for="(item, index3) in banners" :key="index3">
								<swiper-item class="swiper-wrapper" @tap='toNavList(item.linkUrl)' v-if="item">
									<image lazy-load="true" fade-show="true" :src="item.imgUrl" style="width: 100%; height:376rpx;border-radius: 16rpx;" mode="scaleToFill"></image>
								</swiper-item>
							</block>
						</swiper>
					</view>
					<!-- banner结束 -->
				
					<view class="view1">
						<view class="classmain" >
							<view class="tui-tabs" style="width: 100%;">
								<scroll-view id="tab-bar" scroll-with-animation class="tui-scroll-h" :scroll-x="true" :show-scrollbar="false" style="position: unset;">
									<view style="display: flex;width: 100%;flex-wrap: wrap;">
										<view :key='index' v-for="(item, index) in bannerButtonList" style="width:25%; text-align: center;" :id="item.id" :data-current="index" @tap="tabClick(item)">
											<image :src="item.img" style="width:103upx;height:103upx;"></image>
											<view class="nameclore" :class="{ 'tui-tab-item-title-active': tabIndex == item.id }">{{ item.name }}</view>
										</view>
									</view>
								</scroll-view>
							</view>
						</view>
					</view>
					<!--首页公告开始-->
					<view class="clole" style="background:#ffffff;margin:10rpx;padding: 20rpx 0;">
						<swiper v-if="messageList.length > 0" :autoplay="true" :vertical="true" :interval="4000" :circular="true"
						    :indicator-dots="false" style="height: 60upx;line-height: 50upx;color: #000000;margin-left: 20upx;margin-right: 32upx;border-radius: 10upx;width: 95%;padding:12upx 0 0 20upx;">
						    <block v-for="(item, index) in messageList" :key="index">
						     <swiper-item style="height: 60upx;">
						      <view>
						       <image src="../../static/img/laba.png" style="width: 40upx;height: 40upx;margin-top: -5upx;"></image>
						       <text style="position: relative;top: -4px;">{{ item.title }}在《{{item.content}}》中获得{{item.money}}元</text>
						      </view>
						     </swiper-item>
						    </block>
						</swiper>
					</view>	
					<!-- 往期视频 -->
					<view class="view2">
						<view class="view-title" style="overflow: hidden;">
							<image src="../../static/img/index/fire.png" mode="" style="width: 24rpx;height:30rpx;"></image>
							<view style="font-size: 32upx; padding: 0upx 0 0 20upx; color: #000000;">往期视频</view>
							<view style="flex: 1; text-align: right;font-size: 28rpx; color: #000000;padding-right: 20rpx;" @click="goVideolist"> <text style="color:#666666;">查看更多</text><image src="../../static/img/index/right.png" style="width: 20rpx; height: 24rpx;margin-left: 10rpx;" mode=""></image> </view>
						</view>
						<view style="padding: 0rpx 30rpx;" @click="goDetails(videolist)">
							<view class="video-title" style="font-size:30rpx;color: #000000; padding:20rpx 0;">{{videolist.title}}</view>
							<view>
								<image :src="videolist.coverImg"  lazy-load="true" style="width: 100%;height: 300rpx;border-radius: 16rpx;"></image>
							</view>
							<view style="color: #666666;padding: 20rpx 0;overflow: hidden;">
								<text>播放量:{{videolist.view}}</text>
								<text style="float: right;">上传时间:{{videolist.createTime}}</text>
							</view>
						</view>
					</view>
					<view  class="view2" style="padding-bottom: 80upx;" v-if='JingPinList.length > 0'>
						<view class="view-title">
							<image src="../../static/img/index/fire.png" mode="" style="width: 24rpx;height:30rpx;"></image>
							<view style="font-size: 32upx; padding: 0upx 0 0 20upx; color: #000000;">日常活动&赛事</view>
						</view>
						
						<view class="view-con">
							<view class="view2-view" style="display: flex;flex-direction: row;" v-for="(item, index) in JingPinList" :key="index" @click="matchdetail(item)">
								<image :src="item.coverImg" style="border-radius:12rpx;" class="view2-view2"></image>
								<view class="view2-text">
									<view class="text-title">{{item.title}}</view>
									<view class="text-detil">
										<view class="detil-btn">{{item.tag}}</view>
										<view class="btn-center">报名费用:{{item.entryMoney}}元</view>
										<view class="detil-btn" v-if="item.area == 1">微信区</view>
										<view class="detil-btn" v-if="item.area == 2">QQ区</view>
										<view class="detil-btn" v-if="item.eventStatus == 1 && new Date() < Date.parse(item.entryTime)" style="margin-left:12rpx;">未开始</view>
										<view class="detil-btn" v-if="item.eventStatus == 1 && new Date() >= Date.parse(item.entryTime)" style="margin-left:12rpx;">报名中</view>
										<view class="detil-btn" v-if="item.eventStatus == 2 " style="margin-left:12rpx;">比赛中</view>
										
									</view>
									<view class="text-money">
										<view style="white-space:nowrap;overflow:hidden;text-overflow:ellipsis;max-width: 80px;">{{item.gameAwardList[0].title}}:{{item.gameAwardList[0].content}}</view>
										<view class="time-text">
											<image src="../../static/img/index/time.png" style="width: 26rpx;height: 26rpx;" mode=""></image>
											<view style="margin-left: 6rpx;position: relative;top:1px;"> {{item.entryTime}}</view>
										</view>
									</view>
								</view>
							</view>
						</view>
						<!-- 悬浮上拉 -->
						<view class="scroll_top" @tap="topScrollTap" v-bind:class="[scrollTop ? 'active' : '', '']" style="bottom: 56px;"><text
							 class="iconfont icon-shangla"></text></view>	
						<!-- 加载更多提示 -->
						<view class="s-col is-col-24" v-if="JingPinList.length > 0">
							<load-more :loadingType="loadingType" :contentText="contentText"></load-more>
						</view>
					</view>
					
					
				</view>
			</view>
</template>

<script>
	export default {
		data() {
			return {
				taskList: [],
				rate: [],
				userState: '',
				haowuList: [],
				banners: [],
				shareList: [],
				cashList: [],
				videolist:[],
				messageList:[],
				page: 1,
				size: 10,
				tabIndex:0,
				gameTypeId:'',
				scrollTop: false,
				logo: '../../static/img/mao.png',
				isInvitation: 0,
				TabCur: 0,
				scrollLeft: 0,
				JingPinList: [],
				banners3List: [],
				bannerButtonList: [],
				ClassifyList: [],
				loadingType: 0,
				contentText: {
					contentdown: '上拉显示更多',
					contentrefresh: '正在加载...',
					contentnomore: '没有更多数据了'
				}
			};
		},
		onLoad() {
			this.getSelectBanner()
			this.getSelectGame()
			this.videoSelect()
			this.messageSelect()
		},
		methods: {
			topScrollTap: function() {
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				});
			},
			getUserInfo(userId) {
				this.$Request.getT('/user/find?id=' + userId).then(res => {
					if (res.status === 0) {
						this.userState = res.data.member;
					} else {
						uni.showModal({
							showCancel: false,
							title: '登录失败',
							content: res.msg
						});
						this.$queue.logout();
					}
					uni.hideLoading();
				});
			},
			/**
			 * @param {Object} url
			 */
			toNavList: function(url) {
				if (!url) return;
				let userId = this.$queue.getData('userId');
				if (userId) {
					if (url.indexOf('/pages/') !== -1) {
						uni.navigateTo({
							url
						});
					} else {
						//#ifndef H5
						uni.navigateTo({
							url: '/pages/member/webview?url=' + url
						});
						//#endif
						//#ifdef H5
						window.location.href = url;
						//#endif
					}
				} else {
					this.goLoginInfo();
				}
			},
			/**
			 * @param {Object} e tab切换
			 */
			loadList: function(type) {
				this.loadingType = 1;
				uni.showLoading({
					title: '加载中...'
				});
				let page = this.page - 1
				let userId = this.$queue.getData('userId');
				let data = {
					page: page,
					size: this.size,
					gameTypeId: this.gameTypeId
				};
				this.$Request.getT('/gameEvent/userList', data).then(res => {
					if (res.status === 0) {
						if(this.page === 0){
							this.JingPinList = []
						}
						res.data.content.forEach(d =>{
							this.JingPinList.push(d);
						});
						if(this.size === res.data.content.length){
							this.loadingType = 0;
						}else{
							this.loadingType = 3;
						}
						uni.hideLoading();
					} else {
						this.loadingType = 2;
					}
					uni.hideLoading();
					if (type === 'Refresh') {
						uni.stopPullDownRefresh(); // 停止刷新
					}
				});
			},
			tabClick(tab) {
				let userId = this.$queue.getData('userId');
				if (userId) {
					this.gameTypeId = tab.id
					this.tabIndex = tab.id
					this.page = 1
					this.JingPinList = [];
					this.loadList('')
				} else {
					this.goLoginInfo();
				}
			},
			//统一登录跳转
			goLoginInfo() {
				uni.navigateTo({
					url: '/pages/public/loginphone'
				});
			},
			// 公告
			messageSelect(){
				this.$Request.getT(`/gameApply/homeList?page=${this.page-1}&size=${this.size}`).then(res => {
					if (res.status === 0) {
						this.messageList = res.data.content;
					}
				});
			},
			// 往期视频
			videoSelect(){
				this.$Request.getT(`/gameVideo/userList?page=${this.page-1}&size=${this.size}`).then(res => {
					if (res.status === 0) {
						this.videolist = res.data.content[0];
					}
				});
			},
			// 视频详情
			goDetails(item){
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '/pages/video/videoDetails?id=' + item.id
					});
				} else {
					this.goLoginInfo();
				}
			},
			// 视频列表
			goVideolist(){
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '/pages/video/index'
					});
				} else {
					this.goLoginInfo();
				}
			},
			matchdetail(item){
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '../index/match?id=' + item.id +'&userId=' + userId
					});
				} else {
					this.goLoginInfo();
				}
			},
			// 轮播图
			getSelectBanner() {
				this.$Request.getT(`/banner/list?page=${this.page-1}&size=${this.size}&gameTypeId=${this.gameTypeId}`).then(res => {
					if (res.status === 0) {
						this.banners  = res.data
					}
				});
			},
			// 游戏分类
			getSelectGame() {
				this.$Request.getT('/gameType/userList').then(res => {
					if (res.status === 0) {
						this.bannerButtonList  = res.data
						this.gameTypeId = res.data[0].id
						this.tabIndex = this.gameTypeId
						this.JingPinList = [];
						this.loadList('')
					}
				});
			}
		},
		onPageScroll: function(e) {
			this.scrollTop = e.scrollTop > 200;
		},
		onReachBottom: function() {
			this.page = this.page + 1;
			this.loadList('')
		},
		onPullDownRefresh: function() {
			this.page = 1;
			// this.selectTask('Refresh');
		}
	};
</script>

<style lang="scss" scoped>
	@import '../../static/css/index.css';
	
	page{
		background: #EDEDED;
	}
	
	.index-content{
		position: relative;
		background-color: #ededed;
		margin: 0;
		padding: 0;
	}
	.index_bg{
		width: 100%;
		height: 395rpx;
	}
	.index_bg image{
		width: 100%;
		height:100%;
	}
	
	.index-content .swiper-mian{
		padding-top:66rpx;
		background-color: #ffffff;
	}
	.index-content .swiper-mian .swiper-fiexd{
		position: absolute;
		top:94rpx;
		left: 0;
		right: 0;
	}
	.swiper-mian .swiper-container{
		width: 675rpx;
		height: 376rpx;
		margin: 0 auto;
	}
	.view1 .classmain{
		display: flex;
		flex-direction: row;
		flex-wrap: wrap;
		align-items: center;
	}
	.view1 .classmain .classmain-item{
		width: 25%;
		text-align: center;
		margin-bottom: 20rpx;
	}
	
	
	.news_title {
		font-weight: bold;
		color: #F15B6C;
		margin-right: 32upx;
		margin-left: 32upx;
		width: 12upx;
	}

	.icon_search {
		background: #f6f6f6;
		border-radius: 22px;
		height: 69upx;
		line-height: 36px;
		font-size: 14px;
		color: #dcdcdc;
		text-align: left;
		text-indent: 17px;
		position: relative;
		z-index: 1;
		zoom: 1;
		transition: all 0.4s ease 0s;
		transform-origin: center;
	}

	.view1{
		background-color: #FFFFFF;
		margin: 36rpx 0 20rpx 0;
	}

	.view2 {
		background-color: #ffffff;
		width: 100%;
		height: max-content;
		padding:20upx 0;
		border-top: 20rpx solid #EDEDED;
	}

	.view2 .view-title{
		display: flex;
		padding-left: 22rpx;
		align-items: center;
	}
	.view2 .view-con{
		padding:0 22rpx;
		
	}
	
	.view2 .view-con .view2-view{
		background-color: #2e3a4e;
		margin: 20rpx 0;
		border-radius:12rpx;
		padding: 20rpx 26rpx;
		display: flex;
	}
	.view2-view .view2-text{
		flex: 1;
		font-size: 28rpx;
		margin-left: 26rpx;
		color: #fff;
	}
	.view2-view .view2-text .text-detil{
		color: #f2ad44;
		margin: 18rpx 0 10rpx 0;
		display: flex;
	}
	
	.view2-view .view2-text  .text-money{
		display: flex;
		color: #858585;
		align-items: center;
	}
	.text-money .time-text{
		display: flex;
		color: #ffffff;
		align-items: center;
		margin-left: 12rpx;
	}
	.text-money .time-text image{
		margin-right: 2rpx;
	}
	.view2-text .text-detil .btn-center{
		border:2rpx solid #f2ad44;
		margin:0 12rpx;
		border-radius: 6rpx;
		padding: 4rpx 6rpx 0 6rpx;
		font-size: 24rpx;
	}
	.view2-text .text-detil .detil-btn{
		background-color: #f2ad44;
		color: #ffffff;
		border-radius: 6rpx;
		padding: 4rpx 6rpx;
		font-size: 26rpx;
	}
	.view3 {
		width: 100%;
		height: fit-content;
		margin-top: 30upx;
		margin-bottom: 20upx;
		padding-bottom: 20upx;
		padding: 10upx;
	}
	.nameclore{
		color: #333333;
	}
	.nameclore.tui-tab-item-title-active{
		color: #f2ad44;
	}
	.text-green .nameclore{
		color: #f2ad44;
	}
	.nav .cu-item{
		line-height:50rpx;
	}
	.view2-view2 {
		width:144rpx;
		height:144rpx;
	}
</style>
