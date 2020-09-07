<template>
	<view>
		<view class="login-c" v-if="flag">
			<view class="login_box center">
				<text>系统提示</text>
				<view>请先授权登录</view>
				<!-- #ifdef MP-WEIXIN -->
				<button open-type="getUserInfo" @getuserinfo="getuserinfo" withCredentials="true" class="login">授权登录</button>
				<!-- #endif -->
				<!-- #ifdef  MP-ALIPAY  -->
				<button @getAuthorize="onGetAuthorize" scope="userInfo" open-type="getAuthorize" class="login">授权登录</button>
				<!-- #endif -->
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				flag:false
			}
		},
		onReady() {
	
		},
		onLoad() {
			let that=this
			// #ifdef MP-WEIXIN
				uni.login({
					provider:["weixin"],
					success: function(loginRes) {
						console.log(loginRes)
						uni.getUserInfo({
							provider: ["weixin"],
							success: function(infoRes) {
								console.log(infoRes.userInfo);
								if(infoRes.userInfo.nickName){
									uni.switchTab({
										url:'../index/index'
									})
								}else{
									that.flag=true
								}
							},
							fail(err) {
								that.flag=true
							}
						});
					},
				})
			//#endif
	
	
			// #ifdef MP-ALIPAY
				uni.login({
					provider:["alipay"],
					success: function(loginRes) {
						console.log(loginRes)
						uni.getUserInfo({
							provider: ["alipay"],
							success: function(infoRes) {
								let info=JSON.parse(infoRes.response).response
								console.log(info)
								if(info.code=="10000"){
									uni.switchTab({
										url:'../index/index'
									})
								}else{
									that.flag=true
								}
								
							},
							fail(err) {
								that.flag=true
							}
						});
					},
				})
			// #endif
		},
		methods: {
			//支付宝登录
			onGetAuthorize: function(encry) {
				uni.getProvider({
					service: 'oauth',
					success: function(res) {
						console.log(res.provider)
						uni.login({
							provider: res.provider,
							success: function(loginRes) {
								console.log(loginRes)
								uni.getUserInfo({
									provider: res.provider,
									success: function(infoRes) {
										let info=JSON.parse(infoRes.response).response
										console.log(info)
										if(info.code=="10000"){
											uni.switchTab({
												url:'../index/index'
											})
										}else{
											that.flag=true
										}
									},fail() {
										that.flag=true
									}
								});
							},
						});
					}
				});
			},
			// wx登录
			getuserinfo: function() {
				uni.login({
					provider: 'weixin',
					success: function(loginRes) {
						console.log(loginRes);
						// 获取用户信息
						uni.getUserInfo({
							provider: 'weixin',
							success: function(infoRes) {
								console.log(infoRes.userInfo);
								if(infoRes.userInfo.nickName){
									uni.switchTab({
										url:'../index/index'
									})
								}else{
									that.flag=true
								}
							},
							fail(err) {
								that.flag=true
							}
						});
					}
				});
			},
		}
	}
</script>

<style lang="scss">
	.login-c{
		width: 100vw;
		height: 100vh;
		background-color: rgba($color: #000000, $alpha: .5);
		position: fixed;
		top: 0;
		left: 0;
		z-index: 999;
		border-radius: 0px;
		.login_box{
			width: 50vw;
			height: 200upx;
			background-color: #FFFFFF;
			z-index: 1000;
			position: fixed;
			text-align: center;
			padding-top: 20upx;
			box-sizing: border-box;
			overflow: hidden;
			border-radius: 10upx;
			view{
				line-height: 60upx;
			}
			text{
				font-size: 30upx;
				font-weight: 900;
			}
			.login{
				width: 100%;
				background-color: $uni-zhuti-before;
				height: 80upx;
				line-height: 80upx;
				color: #FFFFFF;
				position: absolute;
				border-radius: 0px;
				bottom: 0;
				font-size: 30upx;
				border: 0px;
			}
		}
	}
</style>
