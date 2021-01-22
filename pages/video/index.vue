<template>
	<view class="view2">
		<view style="padding: 0rpx 30rpx;margin-bottom: 20rpx;background-color: #ffffff;" @click="goDetails(videolist[index].id)" v-for="(item,index) in videolist" :key="index">
			<view class="video-title" style="font-size:30rpx;color: #333333; padding:20rpx 0;">{{item.title}}</view>
			<view>
				<image :src="item.coverImg" lazy-load="true" style="width: 100%;height: 300rpx;border-radius: 16rpx;"></image>
			</view>
			<view style="color: #666666;padding: 20rpx 0;overflow: hidden;">
				<text>播放量:{{item.view}}</text>
				<text style="float: right;">上传时间:{{item.createTime}}</text>
			</view>
		</view>
		<!-- 悬浮上拉 -->
		<view class="scroll_top" @tap="topScrollTap" v-bind:class="[scrollTop ? 'active' : '', '']" style="bottom: 56px;"><text
			 class="iconfont icon-shangla"></text></view>
						
		<!-- 加载更多提示 -->
		<view class="s-col is-col-24" v-if="videolist.length > 0">
			<load-more :loadingType="loadingType" :contentText="contentText"></load-more>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				page:1,
				size:5,
				scrollTop: false,
				videolist:[],
				loadingType: 0,
				contentText: {
					contentdown: '上拉显示更多',
					contentrefresh: '正在加载...',
					contentnomore: '没有更多数据了'
				}
			};
		},
		onLoad() {
			this.videoSelect()
		},
		methods: {
			topScrollTap: function() {
				uni.pageScrollTo({
					scrollTop: 0,
					duration: 300
				});
			},
			// 视频列表
			videoSelect(){
				this.$Request.getT(`/gameVideo/userList?page=${this.page-1}&size=${this.size}`).then(res => {
					if (res.status === 0) {
						if(this.page === 0){
							this.videolist = []
						}
						res.data.content.forEach(d =>{
							this.videolist.push(d);
						});
						if(this.size === res.data.content.length){
							this.loadingType = 0;
						}else{
							this.loadingType = 3;
						}
						uni.hideLoading();
					}else{
						this.loadingType = 2;
					}
					uni.hideLoading();
				});
			},
			// 视频详情
			goDetails(id){
				let userId = this.$queue.getData('userId');
				if (userId) {
					uni.navigateTo({
						url: '/pages/video/videoDetails?id=' + id
					});
				} else {
					this.goLoginInfo();
				}
			},
			onPageScroll: function(e) {
				this.scrollTop = e.scrollTop > 200;
			},
			onReachBottom: function() {
				this.page = this.page + 1;
				this.videoSelect('')
			},
			onPullDownRefresh: function() {
				this.page = 1;
				// this.selectTask('Refresh');
			}
		}
	};
</script>

<style lang="scss" scoped>
	@import '../../static/css/index.css';
	.view2{
		background-color: #EDEDED;
	}
</style>
