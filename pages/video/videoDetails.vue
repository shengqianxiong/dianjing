<template>
	<view class="video-mian"> 
		<view>
			<video id="myVideo" :src="videodata.url" style="width: 100% ;" controls></video>
		</view>
		<view class="video-title" style="padding: 20rpx 30rpx;">
			<view style="font-size: 32rpx;color: #000000;">{{videodata.title}}</view>
			<view style="display: flex;align-items: center;margin-top: 10rpx;"> 
				<image src="../../static/img/index/play.png" style="width: 30rpx; height: 30rpx;margin-right: 10rpx;" mode=""></image> {{videodata.view}}
			</view>
		</view>
		<view class="video-content" style="padding: 20rpx 0rpx 100rpx 0;" v-if="commentdata.length>0">
			<view style="font-size: 28rpx;margin-bottom:30rpx;padding-left: 30rpx;">热门评论</view>
			<view class="comment-item" v-for="(item,index) in commentdata" :key="index" style="margin-bottom: 20rpx;border-bottom: 2rpx solid #ECECEC;padding: 20rpx 30rpx;">
				<view style="display: flex;font-size: 28rpx;color: #000000;">
					<image :src="item.headImg" style="width: 60rpx;height: 60rpx;border-radius: 50%;" mode=""></image>
					<view style="margin-left:20rpx;">
						<p style="font-size: 30rpx;margin-bottom:10rpx;">{{item.userName}}</p>
						<p>{{item.createTime}}</p>
					</view>
				</view>
				<view style="margin-top: 20rpx;padding-left: 80rpx;">
					{{item.content}}
				</view>
			</view>
		</view>
		<view class="footer-input" style="background-color: #FFFFFF;padding: 20rpx 0;position: fixed;bottom: 0;left: 0;right: 0;">
			<view class="input-text" style="display: flex;align-items: center;padding: 0 30rpx;">
				<view class="cu-form-group" style="flex: 1;margin-right:40rpx; border: 2rpx solid whitesmoke;border-radius:50rpx;min-height: 80rpx;">
					<input type="text" v-model="content" placeholder="请输入评论内容">
				</view>
				<text @click="send" style="background: #ff9e3c;color: #FFFFFF;font-size: 26rpx; padding:16rpx 40rpx;border-radius: 12rpx;">发送</text>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				page:1,
				size:5,
				content:'',
				commentdata:[],
				videodata:{}
			};
		},
		onLoad(e) {
			this.matchdetail(e.id)
			this.comment(e.id)
		},
		methods:{
			// 详情获取
			matchdetail(id){
				this.$Request.getT(`/gameVideo/find?id=${id}`).then(res => {
					if (res.status === 0) {
						this.videodata = res.data
						this.videoid = res.data.id
					}
				});  
			},
			// 获取评论列表
			comment(id){
				this.$Request.getT(`/ameComment/list?page=${this.page-1}&size=${this.size}&gameVideoId=${id}`).then(res => {
					if (res.status === 0) {
						this.commentdata = res.data.content
					}
				});
			},
			// 发送
			send(){
				if(this.content == ''){
					this.$queue.showToast('请输入评论内容');
					return
				}
				let userId = this.$queue.getData('userId');
				let userName = this.$queue.getData('userName');
				
				console.log(userName)
				
				let headImg = this.$queue.getData('headImg');
				this.$Request.postJson('/ameComment/save',{
					userId: userId,
					userName:userName,
					content:this.content,
					headImg:headImg,
					gameVideoId:this.videoid
				}).then(res => {
					uni.hideLoading();
					if (res.status === 0) {
						this.$queue.showToast("评论成功");
						this.content = ''
						this.comment(this.videoid);
					}else{
						this.$queue.showToast(res.msg);
					}
				});
			}
		}
	}
</script>

<style>
</style>
