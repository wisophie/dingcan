<script>
	export default {
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

<style>
	/*每个页面公共css */
	li {
	    list-style: none;
	}
	/deep/.uni-scroll-view::-webkit-scrollbar {
	        /* 隐藏滚动条，但依旧具备可以滚动的功能 */
	        display: none
	}
</style>
