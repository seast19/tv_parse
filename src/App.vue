<template>
	<Tv></Tv>
</template>

<script>
import Tv from './views/Tv.vue'

export default {
	name: 'app',
	components: {
		Tv
	},
	created() {
		// document.title = '视频解析 - 桑易'
	}
}
</script>

<style>
body {
	font-family: 'Helvetica Neue', Helvetica, 'PingFang SC', 'Hiragino Sans GB',
		'Microsoft YaHei', '微软雅黑', Arial, sans-serif;
}
</style>
