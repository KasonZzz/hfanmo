<template>
	<view>
		<view class="top1">
			下单后技师会在十分钟内联系你哦！
		</view>
		<!-- 技师信息 -->
		<view class="">
			<!-- 头像-信息 -->
			<view class="avatarBox">
				<view class="avatatBox1">
					<image class="avatar" :src="userInfo.massAvatar" mode="aspectFill"></image>
					<view class="infoBox">
						<view class="info1">
							<view class="username">
								{{userInfo.name}}
							</view>
						</view>
						<view class="userInfo">
							<view>
								{{userInfo.tag}}
							</view>
							<view>
								{{userInfo.signature}}
							</view>
						</view>
					</view>
				</view>
			</view>


			<!-- 在线接单-认证档案 -->
			<view class="authBox">
				<view class="order">
					在线接单中
				</view>
				<view class="auth">
					<view class="auth-text">健康码</view>
					<view class="auth-text">实名认证</view>
					<view class="auth-text">资质认证</view>
				</view>

			</view>
			<!-- 距离设计-计费规则 -->
			<view class="mass-place">
				<view class="location">
					距离：8km
				</view>
				<view class="location">
					查看位置
				</view>
				<view class="location">
					车费：双程车费
				</view>
			</view>
			<!-- 技师图片 -->
			<view class="" style="display: flex;">
				<view class="image-item" v-for="(item,index) in userPic" :key="index">
					<view class="image-content">
						<image class="userPic" mode="aspectFill" :src="item.url" @tap="showImg(index)"></image>
					</view>
				</view>
			</view>
		</view>

		<!-- 用户评价 -->
		<view class="">

		</view>
		<!-- 保障协议 -->
		<view class="safeBox">
			<view class="safe">
				未服务实时退
			</view>
			<view class="safe">
				不满意重服务
			</view>
			<view class="safe">
				车费一口价
			</view>
			<view class="safe">
				全场保障
			</view>
		</view>

		<!-- 商品列表 -->
		<view class="goods-list">
			<!-- <view class="tis" v-if="goodsList.length==0">购物车是空的哦~</view> -->
			<view class="row" v-for="(row,index) in goodsList" :key="index">
				<!-- 删除按钮 -->
				<view class="menu" @tap.stop="deleteGoods(row.id)">
					<view class="icon shanchu"></view>
				</view>
				<!-- 商品 -->
				<view class="carrier" :class="[theIndex==index?'open':oldIndex==index?'close':'']"
					@touchstart="touchStart(index,$event)" @touchmove="touchMove(index,$event)"
					@touchend="touchEnd(index,$event)">
					<!-- checkbox -->
					<view class="checkbox-box" @tap="selected(index)">
						<view class="checkbox">
							<view :class="[row.selected?'on':'']"></view>
						</view>
					</view>
					<!-- 商品信息 -->
					<view class="goods-info" @tap="toGoods(row)">
						<view class="img">
							<image :src="row.img" mode="aspectFill"></image>
						</view>
						<view class="info">
							<view class="title">{{row.name}}</view>
							<view class="spec">{{row.spec}}</view>
							<view class="price-number">
								<view class="price">￥{{row.price}}/{{row.time}}分钟</view>
								<view class="number">
									<view class="sub" @tap.stop="sub(index)">
										<view class="icon jian"></view>
									</view>
									<view class="input" @tap.stop="discard">
										<input type="number" v-model="row.number" @input="sum($event,index)" />
									</view>
									<view class="add" @tap.stop="add(index)">
										<view class="icon jia"></view>
									</view>
								</view>
							</view>
						</view>
					</view>
				</view>
			</view>
		</view>
		<!-- 脚部菜单 -->
		<view class="footer" :style="{bottom:footerbottom}">
			<view class="checkbox-box" @tap="allSelect">
				<view class="checkbox">
					<view :class="[isAllselected?'on':'']"></view>
				</view>
				<view class="text">全选</view>
			</view>
			<view class="delBtn" @tap="deleteList" v-if="selectedList.length>0">删除</view>
			<view class="settlement">
				<view class="sum">合计:<view class="money">￥{{sumPrice}}</view>
				</view>
				<view class="btn" @tap="toConfirmation">结算({{selectedList.length}})</view>
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				sumPrice: '0.00',
				selectedList: [],
				isAllselected: false,
				userInfo:{},
				userPic:[],
				goodsList: [{
						id: 1,
						img: '/static/img/1.jpg',
						name: '精油推背',
						spec: '服务姿势：俯卧、仰卧',
						price: '298.00',
						time:'60',
						number: 0,
						selected: false
					},
					{
						id: 2,
						img: '/static/img/2.jpg',
						name: '香薰舒缓spa',
						spec: '服务姿势：俯卧、仰卧',
						price: '298.00',
						time:'60',
						number: 0,
						selected: false
					},
					{
						id: 3,
						img: '/static/img/3.jpg',
						name: '芳香精油spa',
						spec: '服务姿势：俯卧、仰卧',
						price: '298.00',
						time:'60',
						number: 0,
						selected: false
					},
					{
						id: 4,
						img: '/static/img/4.jpg',
						name: '中式全身按摩',
						spec: '服务姿势：俯卧、仰卧',
						price: '298.00',
						time:'60',
						number: 0,
						selected: false
					},
					{
						id: 5,
						img: '/static/img/5.jpg',
						name: '君悦招牌spa',
						spec: '服务姿势：俯卧、仰卧',
						price: '298.00',
						time:'60',
						number: 0,
						selected: false
					}
				],
				//控制滑动效果
				theIndex: null,
				oldIndex: null,
				isStop: false
			}
		},
		onPageScroll(e) {
			//兼容iOS端下拉时顶部漂移
			this.headerPosition = e.scrollTop >= 0 ? "fixed" : "absolute";
			this.headerTop = e.scrollTop >= 0 ? null : 0;
			this.statusTop = e.scrollTop >= 0 ? null : -this.statusHeight + 'px';
		},
		//下拉刷新，需要自己在page.json文件中配置开启页面下拉刷新 "enablePullDownRefresh": true
		onPullDownRefresh() {
			setTimeout(function() {
				uni.stopPullDownRefresh();
			}, 1000);
		},
		onLoad() {
			//兼容H5下结算条位置
			// #ifdef H5
			this.footerbottom = document.getElementsByTagName('uni-tabbar')[0].offsetHeight + 'px';
			// #endif
			// #ifdef APP-PLUS
			this.showHeader = false;
			this.statusHeight = plus.navigator.getStatusbarHeight();
			// #endif
			
			/**
			 * 请求技师详情数据
			 */
			uni.request({
			    url: getApp().globalData.websiteUrl+'spaAppMassagist/queryById', 
			    data: {
			        "id": '1372376037760983042'
			    },
			    success: (res) => {
					this.userInfo = res.data.data;
					this.userPic = res.data.data.spaAppMassPics;
					// alert(JSON.stringify(res.data.data.spaAppMassPics))
			    }
			});
			
			
		},
		methods: {
			showImg(index) {
				uni.previewImage({
					urls: this.userPic,
					current: index,
				});
			},
			//加入商品 参数 goods:商品数据
			joinGoods(goods) {
				/*
				 * 这里只是展示一种添加逻辑，模板并没有做从其他页面加入商品到购物车的具体动作，
				 * 在实际应用上，前端并不会直接插入记录到goodsList这一个动作，一般是更新列表和本地列表缓存。
				 * 一般商城购物车的增删改动作是由后端来完成的,
				 * 后端记录后返回前端更新前端缓存
				 */
				let len = this.goodsList.length;
				let isFind = false; //是否找到ID一样的商品
				for (let i = 0; i < len; i++) {
					let row = this.goodsList[i];
					if (row.id == goods.id) { //找到商品一样的商品
						this.goodsList[i].number++; //数量自增
						isFind = true; //找到一样的商品
						break; //跳出循环
					}
				}
				if (!isFind) {
					//没有找到一样的商品，新增一行到购物车商品列表头部
					this.goodsList[i].unshift(goods);
				}
			},
			//控制左滑删除效果-begin
			touchStart(index, event) {
				//多点触控不触发
				if (event.touches.length > 1) {
					this.isStop = true;
					return;
				}
				this.oldIndex = this.theIndex;
				this.theIndex = null;
				//初始坐标
				this.initXY = [event.touches[0].pageX, event.touches[0].pageY];
			},
			touchMove(index, event) {
				//多点触控不触发
				if (event.touches.length > 1) {
					this.isStop = true;
					return;
				}
				let moveX = event.touches[0].pageX - this.initXY[0];
				let moveY = event.touches[0].pageY - this.initXY[1];

				if (this.isStop || Math.abs(moveX) < 5) {
					return;
				}
				if (Math.abs(moveY) > Math.abs(moveX)) {
					// 竖向滑动-不触发左滑效果
					this.isStop = true;
					return;
				}

				if (moveX < 0) {
					this.theIndex = index;
					this.isStop = true;
				} else if (moveX > 0) {
					if (this.theIndex != null && this.oldIndex == this.theIndex) {
						this.oldIndex = index;
						this.theIndex = null;
						this.isStop = true;
						setTimeout(() => {
							this.oldIndex = null;
						}, 150)
					}
				}
			},
			touchEnd(index, $event) {
				//结束禁止触发效果
				this.isStop = false;
			},
			//控制左滑删除效果-end


			//商品跳转
			toGoods(e) {
				uni.showToast({
					title: '商品' + e.id,
					icon: "none"
				});
				uni.navigateTo({
					url: '../goods/goods'
				});
			},
			//跳转确认订单页面
			toConfirmation() {
				let tmpList = [];
				let len = this.goodsList.length;
				for (let i = 0; i < len; i++) {
					if (this.goodsList[i].selected) {
						tmpList.push(this.goodsList[i]);
					}
				}
				if (tmpList.length < 1) {
					uni.showToast({
						title: '请选择商品结算',
						icon: 'none'
					});
					return;
				}
				uni.setStorage({
					key: 'buylist',
					data: tmpList,
					success: () => {
						uni.navigateTo({
							url: '../../order/confirmation'
						})
					}
				})
			},
			//删除商品
			deleteGoods(id) {
				let len = this.goodsList.length;
				for (let i = 0; i < len; i++) {
					if (id == this.goodsList[i].id) {
						this.goodsList.splice(i, 1);
						break;
					}
				}
				this.selectedList.splice(this.selectedList.indexOf(id), 1);
				this.sum();
				this.oldIndex = null;
				this.theIndex = null;
			},
			// 删除商品s
			deleteList() {
				let len = this.selectedList.length;
				while (this.selectedList.length > 0) {
					this.deleteGoods(this.selectedList[0]);
				}
				this.selectedList = [];
				this.isAllselected = this.selectedList.length == this.goodsList.length && this.goodsList.length > 0;
				this.sum();
			},
			// 选中商品
			selected(index) {
				this.goodsList[index].selected = this.goodsList[index].selected ? false : true;
				let i = this.selectedList.indexOf(this.goodsList[index].id);
				i > -1 ? this.selectedList.splice(i, 1) : this.selectedList.push(this.goodsList[index].id);
				this.isAllselected = this.selectedList.length == this.goodsList.length;
				this.sum();
			},
			//全选
			allSelect() {
				let len = this.goodsList.length;
				let arr = [];
				for (let i = 0; i < len; i++) {
					this.goodsList[i].selected = this.isAllselected ? false : true;
					arr.push(this.goodsList[i].id);
				}
				this.selectedList = this.isAllselected ? [] : arr;
				this.isAllselected = this.isAllselected || this.goodsList.length == 0 ? false : true;
				this.sum();
			},
			// 减少数量
			sub(index) {
				if (this.goodsList[index].number <= 0) {
					return;
				}
				this.goodsList[index].number--;
				this.sum();
			},
			// 增加数量
			add(index) {
				this.goodsList[index].number++;
				this.sum();
			},
			// 合计
			sum(e, index) {
				this.sumPrice = 0;
				let len = this.goodsList.length;
				for (let i = 0; i < len; i++) {
					if (this.goodsList[i].selected) {
						if (e && i == index) {
							this.sumPrice = this.sumPrice + (e.detail.value * this.goodsList[i].price);
						} else {
							this.sumPrice = this.sumPrice + (this.goodsList[i].number * this.goodsList[i].price);
						}
					}
				}
				this.sumPrice = this.sumPrice.toFixed(2);
			},
			discard() {
				//丢弃
			}


		}
	}
</script>
<style lang="scss">
	page {
		position: relative;
		background-color: #fff;
	}

	.top1 {
		text-align: center;
		background-color: #ff5f61;
		color: #FFFFFF;
		padding: 10rpx;
	}

	.avatarBox {
		width: 92%;
		padding: 0 4%;
		display: flex;
		align-items: center;
		// padding-bottom: 120upx;

		.avatatBox1 {
			margin-top: 20rpx;
			display: flex;
			flex-direction: row;
			width: 100%;

			image {
				width: 180rpx;
				height: 180rpx;
				border-radius: 90rpx;
			}

			.infoBox {
				margin-left: 18rpx;
				width: 70%;
				flex-direction: column;

				.username {
					font-weight: 900;
				}

				.userInfo {
					margin-top: 25rpx;
					font-size: 25rpx;
				}
			}
		}

	}

	.mass-place {
		display: flex;
		margin: 10rpx;

		.location {
			margin: 0 15rpx;
		}
	}

	.userPic {
		width: 130rpx;
		height: 130rpx;
		margin: 0rpx 5rpx;
		// background-color: #eeeeee;
	}

	.authBox {
		margin-top: 20rpx;
		width: 92%;
		padding: 0 4%;
		display: flex;
		align-items: center;

		.order {
			width: 180rpx;
			text-align: center;
			color: #1c7de7;
		}

		.auth {
			display: flex;

			.auth-text {
				font-size: 28rpx;
				margin: 0 15rpx 0 15rpx;
			}
		}

	}







	.safeBox {
		margin: 15rpx 0;
		display: flex;
		color: #ff0139;
		font-size: 28rpx;
		justify-content: center;
		border: 1rpx solid #FFF1F1;

		.safe {
			margin: 25rpx 15rpx;
		}
	}


	.checkbox-box {
		display: flex;
		align-items: center;

		.checkbox {
			width: 35upx;
			height: 35upx;
			border-radius: 100%;
			border: solid 2upx #f06c7a;
			display: flex;
			justify-content: center;
			align-items: center;

			.on {
				width: 25upx;
				height: 25upx;
				border-radius: 100%;
				background-color: #f06c7a;
			}
		}

		.text {
			font-size: 28upx;
			margin-left: 10upx;
		}
	}

	.status {
		width: 100%;
		height: 0;
		position: fixed;
		z-index: 10;
		background-color: #fff;
		top: 0;
		/*  #ifdef  APP-PLUS  */
		height: var(--status-bar-height); //覆盖样式
		/*  #endif  */
	}

	.header {
		width: 92%;
		padding: 0 4%;
		height: 100upx;
		display: flex;
		align-items: center;
		position: fixed;
		top: 0;
		z-index: 10;
		background-color: #fff;
		/*  #ifdef  APP-PLUS  */
		top: var(--status-bar-height);

		/*  #endif  */
		.title {
			font-size: 36upx;
		}

	}

	.place {

		background-color: #ffffff;
		height: 100upx;
		/*  #ifdef  APP-PLUS  */
		margin-top: var(--status-bar-height);
		/*  #endif  */
	}

	.goods-list {
		width: 100%;
		padding: 20upx 0 120upx 0;

		.tis {
			width: 100%;
			height: 60upx;
			display: flex;
			justify-content: center;
			align-items: center;
			font-size: 32upx;
		}

		.row {
			width: calc(92%);
			height: calc(22vw + 40upx);
			margin: 20upx auto;

			border-radius: 15upx;
			box-shadow: 0upx 5upx 20upx rgba(0, 0, 0, 0.1);
			display: flex;
			align-items: center;
			position: relative;
			overflow: hidden;
			z-index: 4;
			border: 0;

			.menu {
				.icon {
					color: #fff;
					// font-size: 25upx;
				}

				position: absolute;
				width: 30%;
				height: 100%;
				right: 0;
				display: flex;
				justify-content: center;
				align-items: center;
				background-color: red;
				color: #fff;
				z-index: 2;
			}

			.carrier {
				@keyframes showMenu {
					0% {
						transform: translateX(0);
					}

					100% {
						transform: translateX(-30%);
					}
				}

				@keyframes closeMenu {
					0% {
						transform: translateX(-30%);
					}

					100% {
						transform: translateX(0);
					}
				}

				&.open {
					animation: showMenu 0.25s linear both;
				}

				&.close {
					animation: closeMenu 0.15s linear both;
				}

				background-color: #fff;

				.checkbox-box {
					padding-left: 20upx;
					flex-shrink: 0;
					height: 22vw;
					margin-right: 20upx;
				}

				position: absolute;
				width: 100%;
				padding: 0 0;
				height: 100%;
				z-index: 3;
				display: flex;
				align-items: center;

				.goods-info {
					width: 100%;
					display: flex;
					padding-right: 20upx;

					.img {
						width: 22vw;
						height: 22vw;
						border-radius: 10upx;
						overflow: hidden;
						flex-shrink: 0;
						margin-right: 10upx;

						image {
							width: 22vw;
							height: 22vw;
						}
					}

					.info {
						width: 100%;
						height: 22vw;
						overflow: hidden;
						display: flex;
						flex-wrap: wrap;
						position: relative;

						.title {
							width: 100%;
							font-size: 28upx;
							display: -webkit-box;
							-webkit-box-orient: vertical;
							-webkit-line-clamp: 2;
							// text-align: justify;
							overflow: hidden;
						}

						.spec {
							font-size: 20upx;
							background-color: #f3f3f3;
							color: #a7a7a7;
							height: 30upx;
							display: flex;
							align-items: center;
							padding: 0 10upx;
							border-radius: 15upx;
							margin-bottom: 20vw;
						}

						.price-number {
							position: absolute;
							width: 100%;
							bottom: 0upx;
							display: flex;
							justify-content: space-between;
							align-items: flex-end;
							font-size: 28upx;
							height: 60upx;

							.price {
								font-size: 20rpx;
								color: #ff0139;
							}

							.number {
								display: flex;
								justify-content: center;
								align-items: flex-end;

								.input {
									width: 60upx;
									height: 60upx;
									margin: 0 10upx;
									background-color: #f3f3f3;

									input {
										width: 60upx;
										height: 60upx;
										display: flex;
										justify-content: center;
										align-items: center;
										text-align: center;
										font-size: 26upx;
									}
								}

								.sub,
								.add {
									width: 45upx;
									height: 45upx;
									background-color: #f3f3f3;
									border-radius: 5upx;

									.icon {
										font-size: 22upx;
										width: 45upx;
										height: 45upx;
										display: flex;
										justify-content: center;
										align-items: center;

									}
								}
							}
						}
					}
				}
			}
		}
	}

	.footer {
		width: 92%;
		padding: 0 4%;
		background-color: #fbfbfb;
		height: 100upx;
		display: flex;
		justify-content: space-between;
		align-items: center;
		font-size: 28upx;
		position: fixed;
		bottom: 0upx;
		z-index: 5;

		.delBtn {
			border: solid 1upx #f06c7a;
			color: #f06c7a;
			padding: 0 30upx;
			height: 50upx;
			border-radius: 30upx;
			display: flex;
			justify-content: center;
			align-items: center;
		}

		.settlement {
			width: 60%;
			display: flex;
			justify-content: flex-end;
			align-items: center;

			.sum {
				width: 50%;
				font-size: 28upx;
				margin-right: 10upx;
				display: flex;
				justify-content: flex-end;

				.money {
					font-weight: 600;
				}
			}

			.btn {
				padding: 0 30upx;
				height: 50upx;
				background-color: #f06c7a;
				color: #fff;
				display: flex;
				justify-content: center;
				align-items: center;

				border-radius: 30upx;
			}
		}
	}
</style>
