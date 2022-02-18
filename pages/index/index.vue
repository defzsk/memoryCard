<template>
	<view class="container">
		<view v-if="!this.getCardDataList().length" class="card" @click="showCardContent(introduce.content)">
			<view class="title">
				{{introduce.title}}
			</view>
			<view class="content">
				{{introduce.content}}
			</view>
		</view>
		<view class="card" v-for="(item, index) in cardDataList" :key="index" @click="showCardContent(item.content)">
			<view class="title">
				{{item.title}}
			</view>
			<view class="content">
				{{item.content}}
			</view>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				cardDataList: [],
				introduce: {
					title: '使用说明',
					content: '产品使用说明书是介绍产品安装、调试、使用、维修保养的应用文本。它是一种有关产品知识和使用须知的科技应用文体。一般按照用户的认知习惯和认知程度，按照一定次序准确阐述。 它广泛地使用在各类产品上，它是科技应用文中使用范围最广、适用面最大的一种文体。不同类型的产品，对产品说明书的要求也不一样，生活用产品及部分医药品的说明书内容简单，篇幅较短，具有广告色彩；生产、科研用产品、专用产品、仪器设备产品的说明书内容较为详细，具有一定格式。'
				}
			}
		},
		onShow() {
			this.cardDataList = this. getCardDataList()
			console.log(this.cardDataList)
		},
		methods: {
          // 获取本地缓存卡片数组
          getCardDataList() {
            try {
                 const value = JSON.parse(uni.getStorageSync('cardDataList'))
                 if (value) {
                     return value
                }
             } catch (e) {
          	  return []
             }
          },
		  // 展示卡片内容
		  showCardContent(content) {
		  				   console.log(content)
		  				   uni.navigateTo({
		  				       url: '../cardContent/index?content='+ content
		  				   })
		  },
		}
	}
</script>

<style scope>
	.container {
		height: 100vh; 
		background: #022b36;
	}
	.card {
		height: 300rpx;
		background: #fff;
		border-radius: 20rpx;
		margin:0 15rpx 20rpx 15rpx;
		color: #000;
		padding: 20rpx;
	}
	.title {
		font-size: 50rpx;
		text-align: center;
	}
	.content {
		/* background: #007AFF; */
		height: 220rpx;
		/* white-space:nowrap; */
		overflow:hidden;
		/* text-overflow:ellipsis; */
	}
</style>
