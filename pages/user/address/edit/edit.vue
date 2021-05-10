<template>
	<view>
		<view class="content">
			<view class="row">
				<view class="nominal">
					收件人
				</view>
				<view class="input">
					<input placeholder="请输入收件人姓名" type="text" v-model="name" />
				</view>
			</view>
			<view class="row">
				<view class="nominal">
					电话号码
				</view>
				<view class="input">
					<input placeholder="请输入收件人电话号码" type="text" v-model="tel" />
				</view>
			</view>
			<view class="row">
				<view class="nominal">
					所在地区
				</view>
				<view class="input" @tap="chooseCity">
					{{region.label}}
				</view>

			</view>
			<view class="row">
				<view class="nominal">
					详细地址
				</view>
				<view class="input">
					<textarea v-model="detailed" auto-height="true" placeholder="输入详细地址"></textarea>
				</view>
			</view>
			<view class="row">
				<view class="nominal">
					设置默认地址
				</view>
				<view class="input switch">
					<switch color="#f06c7a" :checked="isDefault" @change=isDefaultChange />
				</view>
			</view>
			<view class="row" v-if="editType=='edit'" @tap="del">
				<view class="del">
					删除地址
				</view>
			</view>
		</view>
		<view class="save" @tap="save">
			<view class="btn">
				保存地址
			</view>
		</view>
		<mpvue-city-picker :themeColor="themeColor" ref="mpvueCityPicker" :pickerValueDefault="cityPickerValue"
			@onCancel="onCancel" @onConfirm="onConfirm"></mpvue-city-picker>
	</view>
</template>

<script>
	import mpvueCityPicker from '@/components/mpvue-citypicker/mpvueCityPicker.vue'
	export default {
		components: {
			mpvueCityPicker
		},
		data() {
			return {
				editType: 'edit',
				userId: '',
				id: '',
				name: '',
				tel: '',
				detailed: '',
				isDefault: false,
				cityPickerValue: [0, 0, 1],
				themeColor: '#007AFF',
				region: {
					label: "请点击选择地址",
					value: [],
					cityCode: ""
				}
			};
		},
		methods: {
			onCancel(e) {
				console.log(e)
			},
			chooseCity() {
				this.$refs.mpvueCityPicker.show()
			},
			onConfirm(e) {
				this.region = e;
				this.cityPickerValue = e.value;
			},
			isDefaultChange(e) {
				this.isDefault = e.detail.value;
			},
			del() {
				uni.showModal({
					title: '删除提示',
					content: '你将删除这个收货地址',
					success: (res) => {
						if (res.confirm) {
							uni.request({
								method:"POST",
								url: getApp().globalData.websiteUrl + 'spaAppDomicile/delById',
								data: {
									"id": this.id
								},
								success: (res) => {
									console.log(res);
									uni.navigateBack();
								},
							});
						} else if (res.cancel) {
							console.log('用户点击取消');
						}
					}
				});

			},
			save() {
				console.log(this.userId);
				let data = {
					"userId":this.userId,
					"name": this.name,
					"headCode": this.name.substr(0, 1),
					"phoneNum": this.tel,
					"area": JSON.stringify(this.region),
					"address": this.detailed,
					"status": this.isDefault,
					"type": this.editType,
				}
				if (this.editType == 'edit') {
					data.id = this.id
				}
				if (!data.name) {
					uni.showToast({
						title: '请输入收件人姓名',
						icon: 'none'
					});
					return;
				}
				if (!data.phoneNum) {
					uni.showToast({
						title: '请输入收件人电话号码',
						icon: 'none'
					});
					return;
				}
				if (!data.address) {
					uni.showToast({
						title: '请输入收件人详细地址',
						icon: 'none'
					});
					return;
				}
				if (data.area == 0) {
					uni.showToast({
						title: '请选择收件地址',
						icon: 'none'
					});
					return;
				}
				uni.showLoading({
					title: '正在提交'
				})
				console.log(data);
				uni.request({
					method: "POST",
					url: getApp().globalData.websiteUrl + 'spaAppDomicile/add',
					data: data,
					success: (res) => {
						console.log(res);
						uni.hideLoading();
						uni.navigateBack();
					},
				});

				// //实际应用中请提交ajax,模板定时器模拟提交效果
				// setTimeout(() => {
				// 	uni.setStorage({
				// 		key: 'saveAddress',
				// 		data: data,
				// 		success() {
				// 			uni.hideLoading();
				// 			uni.navigateBack();
				// 		}
				// 	})
				// }, 300)


			},
			getUser(e) {
				uni.request({
					url: getApp().globalData.websiteUrl + 'redis/getUserInfo',
					data: {
						"key": e
					},
					success: (res) => {
						console.log(res);
						// this.user = res.data;
						this.userId = res.data.id;
					},
				});
			},
			getById(id){
				uni.request({
					method:"POST",
					url: getApp().globalData.websiteUrl + 'spaAppDomicile/getById',
					data: {
						"id": id
					},
					success: (res) => {
						console.log(111);
						console.log(res);
						var data = res.data.data;
						this.tel = data.phoneNum;
						this.name = data.name;
						this.region = JSON.parse(data.area);
						this.detailed = data.address;
						this.isDefault = JSON.parse(data.status);
						// this.userId = res.data;
					},
				});
			}
			
		},
		onLoad(e) {
			//获取传递过来的参数
			this.editType = e.type;
			var UserInfo =localStorage.getItem("userInfo");
			this.getUser(UserInfo)
			if(e.type === "edit"){
				console.log(e.id);
				this.getById(e.id);
				this.id = e.id;
			}
			
		},
		onBackPress() {
			if (this.$refs.mpvueCityPicker.showPicker) {
				this.$refs.mpvueCityPicker.pickerCancel();
				return true;
			}
		},
		onUnload() {
			if (this.$refs.mpvueCityPicker.showPicker) {
				this.$refs.mpvueCityPicker.pickerCancel()
			}
		}
	};
</script>
<style lang="scss">
	.save {
		view {
			display: flex;
		}

		position: fixed;
		bottom: 0;
		width: 100%;
		height: 120upx;
		display: flex;
		justify-content: center;
		align-items: center;

		.btn {
			box-shadow: 0upx 5upx 10upx rgba(0, 0, 0, 0.4);
			width: 70%;
			height: 80upx;
			border-radius: 80upx;
			background-color: #f06c7a;
			color: #fff;
			justify-content: center;
			align-items: center;

			.icon {
				height: 80upx;
				color: #fff;
				font-size: 30upx;
				justify-content: center;
				align-items: center;
			}

			font-size: 30upx;
		}
	}

	.content {
		display: flex;
		flex-wrap: wrap;

		view {
			display: flex;
		}

		.row {
			width: 94%;

			margin: 0 3%;
			border-top: solid 1upx #eee;

			.nominal {
				width: 30%;
				height: 120upx;
				font-weight: 200;
				font-size: 30upx;
				align-items: center;
			}

			.input {
				width: 70%;
				padding: 20upx 0;
				align-items: center;
				font-size: 30upx;

				&.switch {
					justify-content: flex-end;
				}

				.textarea {
					margin: 20upx 0;
					min-height: 120upx;
				}
			}

			.del {
				width: 100%;
				height: 100upx;
				justify-content: center;
				align-items: center;
				font-size: 36upx;
				color: #f06c7a;
				background-color: rgba(255, 0, 0, 0.05);
				border-bottom: solid 1upx #eee;
			}
		}
	}
</style>
