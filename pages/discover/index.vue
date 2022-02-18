<template>
	<view class="discover-container">
		<button @click="open2">打开弹窗</button>
		
	</view>
</template>

<script>
	export default {
		data(){
			return{
			  value1 :''
			}
		},
	    methods: {
	         open2(){
	            uni.requestSubscribeMessage({
	              tmplIds: [''],
	              success (res) { }
	            })
	              }
	    }
	}

</script>

<style scoped>
 .discover-container {
	 height: 100vh;
	 background: #fff;
 }
</style>
