<template>
	<view>
		<view class="logo">
			<view class="img">
				<image mode="widthFix" src="../../static/img/logo.png"></image>
			</view>
		</view>
		<view class="form re">
			<view class="username">
				<view class="get-code" :style="{'color':getCodeBtnColor}" @click.stop="getCode()">{{getCodeText}}</view>
				<input placeholder="请输入手机号" v-model="phoneNumber" placeholder-style="color: rgba(255,255,255,0.8);" />
			</view>
			<view class="code">
				<input placeholder="请输入验证码" v-model="code" placeholder-style="color: rgba(255,255,255,0.8);" />
			</view>
			<view class="btn" @tap="doReg">立即登录</view>
			<view class="res">
				<view><span style="color: red;">*</span>未注册用户将自动注册</view>
			</view>
		</view>

	</view>
</template>

<script>
	import md5 from "@/common/SDK/md5.min.js";
	export default {
		data() {
			return {
				key: "",
				phoneNumber: "",
				code: '',
				passwd: "",
				getCodeText: '获取验证码',
				getCodeBtnColor: "#ffffff",
				getCodeisWaiting: false
			}
		},
		onLoad() {
			this.getUserInfo();
		},
		methods: {
			getUserInfo() {
				uni.getStorage({
					key: "userInfo",
					success(e) {
						var data = e.data //这就是你想要取的token
						if (data != undefined && data != "") {
							uni.request({
								url: getApp().globalData.websiteUrl + 'redis/getUserInfo',
								data: {
									"key": data
								},
								success: (res) => {
									uni.setStorage({
										key: "userInfo",
										data: "userInfo_" + res.data.mobile
									})
									uni.switchTab({
										url: '../tabBar/home/home'
									});
								}
							});
						}
					}
				})
			},


			Timer() {},
			getCode() {

				uni.hideKeyboard()
				if (this.getCodeisWaiting) {
					return;
				}
				if (!(/^1(3|4|5|6|7|8|9)\d{9}$/.test(this.phoneNumber))) {
					uni.showToast({
						title: '请填写正确手机号码',
						icon: "none"
					});
					return false;
				}
				this.getCodeText = "发送中..."
				this.getCodeisWaiting = true;
				this.getCodeBtnColor = "rgba(255,255,255,0.5)"
				console.log(111);
				/**
				 * 获取验证码
				 */
				uni.request({
					method: "POST",
					url: getApp().globalData.websiteUrl + 'spaAppUser/getSmsCode',
					data: {
						"mobile": this.phoneNumber,
					},
					success: (res) => {
						console.log("验证码发送成功，验证码状态：" + res);
						uni.showToast({
							title: '验证码已发送',
							icon: "none"
						});
						this.setTimer();
					},
					fail(res) {
						console.log(res);
					}
				});
			},
			/**
			 * 设置定时器
			 */
			setTimer() {
				let holdTime = 60;
				this.getCodeText = "重新获取(60)"
				this.Timer = setInterval(() => {
					if (holdTime <= 0) {
						this.getCodeisWaiting = false;
						this.getCodeBtnColor = "#ffffff";
						this.getCodeText = "获取验证码"
						clearInterval(this.Timer);
						return;
					}
					this.getCodeText = "重新获取(" + holdTime + ")"
					holdTime--;

				}, 1000)
			},

			doReg() {
				uni.hideKeyboard()
				//模板示例部分验证规则
				if (!(/^1(3|4|5|6|7|8|9)\d{9}$/.test(this.phoneNumber))) {
					uni.showToast({
						title: '请填写正确手机号码',
						icon: "none"
					});
					return false;
				}

				/**
				 * 获取验证码
				 */
				uni.request({
					method: "POST",
					url: getApp().globalData.websiteUrl + 'spaAppUser/register',
					data: {
						"mobile": this.phoneNumber,
						"smsCode": this.code
					},
					success: (res) => {

						console.log(res);
						uni.showToast({
							title: '登录成功',
							icon: "none"
						});
						uni.setStorage({
							key: "userInfo",
							data: "userInfo_" + res.data.data.mobile
						})
						// localStorage.setItem("userInfo", );
						console.log(111);
						uni.switchTab({
							url: '../tabBar/home/home'
						})
					},
					fail(res) {
						console.log(res);
					}
				});

				// //示例验证码，实际使用中应为请求服务器比对验证码是否正确。
				// if (this.code != 1234) {
				// 	uni.showToast({
				// 		title: '验证码不正确',
				// 		icon: "none"
				// 	});
				// 	return false;
				// }
				// uni.showLoading({
				// 	title: '提交中...'
				// })



				// //模板示例把用户注册信息储存在本地，实际使用中请替换为上传服务器。
				// setTimeout(() => {
				// 	uni.getStorage({
				// 		key: 'UserList',
				// 		success: (res) => {
				// 			//增加记录，密码md5
				// 			res.data.push({
				// 				username: this.phoneNumber,
				// 				passwd: md5(this.passwd)
				// 			})
				// 			uni.setStorage({
				// 				key: 'UserList',
				// 				data: res.data,
				// 				success: function() {
				// 					uni.hideLoading()
				// 					uni.showToast({
				// 						title: '注册成功',
				// 						icon: "success"
				// 					});
				// 					setTimeout(function() {
				// 						uni.navigateBack();
				// 					}, 1000)
				// 				}
				// 			});
				// 		},
				// 		fail: (e) => {
				// 			uni.hideLoading()
				// 			console.log('error');
				// 			//新建UserList
				// 			uni.setStorage({
				// 				key: 'UserList',
				// 				data: [{
				// 					username: this.phoneNumber,
				// 					passwd: md5(this.passwd)
				// 				}],
				// 				success: function() {
				// 					uni.hideLoading()
				// 					uni.showToast({
				// 						title: '注册成功',
				// 						icon: "success"
				// 					});
				// 					setTimeout(function() {
				// 						uni.navigateBack();
				// 					}, 1000)
				// 				},
				// 				fail: function(e) {
				// 					console.log('set error:' + JSON.stringify(e));
				// 				}
				// 			});
				// 		}
				// 	});
				// }, 1000)
			},
			// toLogin(){ 
			// 	uni.hideKeyboard()
			// 	uni.redirectTo({url: 'login'});
			// 	uni.navigateBack();
			// }
		}
	}
</script>

<style lang="scss">
	@import "../../static/css/login.scss";
</style>
