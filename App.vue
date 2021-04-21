<script>
	export default {
		globalData: {
			websiteUrl : 'http://192.168.0.106:8088/'
		},
		onLaunch: function() {
			console.log('App Launch')
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
