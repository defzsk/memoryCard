<template>
	<view class="cardContent">
		<view class="nav">
			   <uni-segmented-control  :current="current" :values="items" @clickItem="onClickItem" styleType="text" activeColor="#4cd964"></uni-segmented-control>
			         <view class="content">
			             <view v-show="current === 0">
			                 {{cardContent}}
			             </view>
			             <view v-show="current === 1">
			                 {{replaceCardContent}}
			             </view>
						 <view v-show="current === 2">
						     {{replaceCardContent2}}
						 </view>
			         </view>
		</view>
		
	</view>
</template>

<script>
	export default {
		data() {
			return {
				cardContent: '',
				replaceCardContent: '',
				replaceCardContent2: '',
				current: 0,
				items: ['原文','模版一','模版二']
			}
		},
	    onLoad: function (option) { //option为object类型，会序列化上个页面传递的参数
	        console.log(option);
			 this.cardContent = option.content//打印出上个页面传递的参数。
			 console.log(this.cardContent)
	    },
		onShow() {
			console.log(this.replaceDemo())
			this.replaceCardContent = this.replaceDemo()
			this.replaceCardContent2 = this.replaceDemo2()
			
			
		},
		methods: {
			// 替换模版1（替换所有索引为为偶数的字符串为x
			replaceDemo(){  
			    // var s = "我爱北京天安门，天安门上太阳升";  
				var s = this.cardContent
			    var regex = /./g;  
			    var index = 0;  
			    s=s.replace(regex,function(){index++;return index%2?'X':arguments[0]});  
			    return s;  
			},
			// 替换模版2（替换所有索引为为偶数的字符串为x
			replaceDemo2(){
			    // var s = "我爱北京天安门，天安门上太阳升";  
				var s = this.cardContent
			    var regex = /./g;  
			    var index = 1;  
			    s=s.replace(regex,function(){index++;return index%2?'_':arguments[0]});  
			    return s;  
			},
			// nav选项点击
			onClickItem(e) {
				console.log(e.currentIndex)
				this.current = e.currentIndex
			}
		}
	}
</script>

<style scoped>
	.cardContent {
		background: #E5E7EB;
		color: #000;
		height: 100vh;
		font-size: 40rpx;
	}
	.segmented-control__text {
		font-size: 35rpx;
	}
</style>
