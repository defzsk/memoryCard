<template>
	<view class="card-box">
		<view class="card" v-for="(item, index) in cardList" :key="index" @longpress="showPopup(index)" @click="showCardContent(item.content)">
			<view class="title">
				{{item.title}}
			</view>
			<view class="content">
				{{item.content}}
			</view>
		</view>
		<!-- 底部弹出框 -->
		<fui-bottom-popup :show="show" @close="closePopup" zIndex="9999999" background="#2e2e2e">
			<view class="fui-custom__wrap">
				<!-- <view class="pigeonhole">
					<fui-button  background="#777" color="#fff" radius="0">移动到未归档</fui-button>
				</view> -->
				<view class="pigeonhole">
					<fui-button background="#777" color="#fff" radius="0" @click="deleteCard">删除卡片</fui-button>
				</view>
				<view class="cancel">
					<fui-button background="#777" color="#fff" radius="0" @click="cancelSelect">取消</fui-button>
				</view>		
			</view>
		</fui-bottom-popup>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				index: '',
				num: '',
				cardList: [],
				show: false,
				cardDataList: [],
				newCardDataList: []
			}
		},
	    onLoad: function (option) { //option为object类型，会序列化上个页面传递的参数
	        console.log(option);
			 this.index = option.index//打印出上个页面传递的参数。
			 console.log(this.index)
	    },
		onShow() {
			console.log(this.index)
			this.cardList = this.getDataList()[this.index].children
			console.log(this.cardList)
		    this.cardDataList = this.getCardDataList()
			console.log(this.cardDataList)
		},
		methods: {
			getDataList() {
			  try {
			       const value = JSON.parse(uni.getStorageSync('dataList'))
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
			   // 长按弹出底部选择框
			   showPopup(num) {
				   this.num = num
				   this.show = true
			   },
			   closePopup(type) {
			   	this.num = ''
			   	this.show = false
			   },
			   // 取消选择删除卡片
			   cancelSelect() {
				   this.closePopup()
			   },
			   // 删除卡片
			   deleteCard() {
				   console.log(this.getDataList())	
				   const number = this.cardList.splice(this.num, 1)
				   console.log(number)
				   let id = number[0].id
				   console.log(id)
				   console.log(this.cardDataList)
				   this.newCardDataList = this.cardDataList.filter(item => {
					   if (item.id !== id) {
						   return item
					   }
				   }) 
				   console.log(this.newCardDataList)
				  try {
				      uni.setStorageSync('cardDataList', JSON.stringify(this.newCardDataList))
				  } catch (e) {
				      // error
				  }
				   this.closePopup()
			   },
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
			   }
			   
		}
	}
</script>

<style scoped>
	.card-box {
		background: #E5E7EB;
		margin-top: 20rpx;
		/* height: 100vh; */
	}
	.card {
		height: 300rpx;
		background: #fff;
		border-radius: 20rpx;
		margin:0 15rpx 25rpx 15rpx;
		padding-bottom: 15rpx;
		color: #000;
		overflow:hidden;
		/* padding: 20rpx; */
		box-shadow: 5px 3px 8px 0px rgb(43 145 255 / 78%)
	}
	.title {
		background: #1ab16d;
		font-size: 50rpx;
		height: 80rpx;
		line-height: 80rpx;
		color: #fff;
		text-align: center;
		border-radius:  20rpx 20rpx 0 0 ;
	}
	.content {
		/* background: #007AFF; */
		height: 220rpx;
		/* white-space:nowrap; */
		/* overflow:hidden; */
		 padding: 20rpx;
		/* text-overflow:ellipsis; */
	}
</style>
