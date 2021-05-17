<template>
	<view>
		<view class="content">
			<view class="list">
				<view class="row">
					<view class="title">头像</view>
					<view class="right">
						<view class="tis">
							<image :src="user.avatar" mode="widthFix"></image>
						</view>
						<view class="icon xiangyou"></view>
					</view>
				</view>
				<view class="row">
					<view class="title">昵称</view>
					<view class="right">
						<view class="tis">{{user.nickName}}</view>
						<view class="icon xiangyou"></view>
					</view>
				</view>

				<view class="row">
					<view class="title">性别</view>
					<view class="right">
						<view class="tis">{{user.sex}}</view>
						<view class="icon xiangyou"></view>
					</view>
				</view>

				<view class="row">
					<view class="title">个性签名</view>
					<view class="right">
						<view class="tis">{{user.signature}}</view>
						<view class="icon xiangyou"></view>
					</view>
				</view>
				<view class="row" @tap="toAddress()">
					<view class="title">地址管理</view>
					<view class="right">
						<view class="tis"></view>
						<view class="icon xiangyou"></view>
					</view>
				</view>
			</view>

			<view class="list">
				<view class="row">
					<view class="title">版本升级</view>
					<view class="right">
						<view class="tis">v1.0.0</view>
						<view class="icon xiangyou"></view>
					</view>
				</view>
				<view class="row" @tap="clearCahche()">
					<view class="title">清除缓存</view>
					<view class="right">
						<view class="tis"></view>
						<view class="icon xiangyou"></view>
					</view>
				</view>
				<view class="row">
					<view class="title">问题反馈</view>
					<view class="right">
						<view class="tis"></view>
						<view class="icon xiangyou"></view>
					</view>
				</view>
			</view>
		</view>
	</view>
</template>

<script>
	let that = null;
	export default {
		data() {
			return {
				user: {},
			};
		},
		onLoad() {
			//将this赋值
			that = this;
			//从缓存中拿到用户信息
			uni.getStorage({
				key: "userInfo",
				success(e) {
					//用户信息赋值
					that.getUser(e.data);
				}
			})
		},
		methods: {
			/**
			 * 获取用户信息
			 */
			getUser(e) {
				uni.request({
					url: getApp().globalData.websiteUrl + 'redis/getUserInfo',
					data: {
						"key": e
					},
					success: (res) => {
						console.log(res);
						this.user = res.data;
					},
				});
			},
			/**
			 * 跳转地址页面
			 */
			toAddress() {
				uni.navigateTo({
					url: '../address/address'
				})
			},
			/**
			 * 清理缓存
			 */
			clearCahche() {
				localStorage.clear();
				uni.clearStorageSync();
				uni.showToast({
					title: '清除缓存成功',
					icon: "success"
				});
			}

		}
	}
</script>

<style lang="scss">
	page {
		background-color: #f3f3f3;
	}

	.icon {
		font-size: 30upx;

	}

	.content {
		padding-bottom: 20upx;

		.list {
			width: 96%;
			padding-left: 4%;
			background-color: #fff;
			margin-bottom: 20upx;

			.row {
				widows: 100%;
				min-height: 90upx;
				display: flex;
				align-items: center;
				justify-content: space-between;
				border-bottom: solid 1upx #eee;

				&:last-child {
					border-bottom: none;
				}

				.title {
					font-size: 32upx;
					color: #333;
				}

				.right {
					display: flex;
					align-items: center;
					color: #999;

					.tis {
						font-size: 26upx;
						margin-right: 5upx;
						max-height: 120upx;

						image {
							width: 100upx;
							height: 100upx;
							border-radius: 100%;
							margin: 10upx 0;
						}
					}

					.icon {
						width: 40upx;
						color: #cecece;
					}
				}

			}
		}
	}
</style>
