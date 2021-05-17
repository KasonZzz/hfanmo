<script>
	export default {
		globalData: {
			// websiteUrl : 'http://47.117.116.96:8088/'
			// websiteUrl: 'http://192.168.43.58:8088/'
			websiteUrl: 'http://192.168.0.93:8088/'
		},
		onLaunch: function() {
			//app自动更新
			// #ifdef APP-PLUS  
			plus.runtime.getProperty(plus.runtime.appid, function(widgetInfo) {
				uni.request({
					method:"POST",
					url: getApp().globalData.websiteUrl + '/spaAppVersion/update',
					data: {
						version: widgetInfo.version,
						name: widgetInfo.name
					},
					success: (result) => {
						var data = result.data;
						if (data.update) {
							uni.downloadFile({
								url: data.wgtUrl,
								success: (downloadResult) => {
									if (downloadResult.statusCode === 200) {
										plus.runtime.install(downloadResult
											.tempFilePath, {
												force: false
											},
											function() {
												console.log('install success...');
												plus.runtime.restart();
											},
											function(e) {
												console.error('install fail...');
											});
									}
								}
							});
						}
					}
				});
			});
			// #endif
		},
		onShow: function() {
			console.log('App Show')
			
		},
		onHide: function() {
			console.log('App Hide')
		}
	}
</script>

<style lang="scss">
	/*每个页面公共css */
	@import "/static/iconfont/font.scss";
	@import url("//at.alicdn.com/t/font_2351741_8lr8ym8raq2.css");
</style>
