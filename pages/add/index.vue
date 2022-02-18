<template>
	<view class="add">
		<view class="title">
			<fui-input borderTop placeholder="请输入卡片名称" size="45rpx"v-model="cardData.title" ></fui-input>
		</view>
		<view class="content">
			<fui-textarea isCounter placeholder="请输入内容" v-model="cardData.content" height="750rpx" maxlength="2800"></fui-textarea>
		</view>
		<view class="btn">
			<!-- 多列选择器 -->
			<picker  mode="multiSelector" :range="array" range-key="categoryName"  @change="picker2" :value='value'>
				<fui-button background="#1ab16d"@click="save">保存卡片</fui-button>
			</picker>
		</view>
		<!--剪贴板对话框 -->
		<fui-dialog :show="show" :content="pp" maskClosable @click="onClick" @close="onClose"></fui-dialog>
		<!--保存失败对话框 -->
			<fui-dialog :show="showSave" title="保存失败" :content="saveCard" maskClosable @click="onSave" @close="onfailClose"></fui-dialog>
			<!-- 保存成功轻提示 -->
			<fui-toast ref="toast">
				<view class="fui-toast__custom">
					<fui-icon name="checkbox" color="#fff"></fui-icon>
					<text class="fui-toast__txt">保存成功</text>
				</view>
			</fui-toast>
		</view>
	</view>
</template>

<script>
	export default {
		data(){
			return{
			  categoryList: [],
			  cardDataList: [],
			  cardData: {
				  title: '',
				  content: '',
				  cid: '',
				  id: ''
			  },
			  
			  pp: '在剪贴板发现内容，是否粘贴',
			  saveCard: '卡片内容为空',
			  show: false,
			  showSave: false,
			  details: '',
			   array:[[],[{categoryName: '中文' },{categoryName: '英文' }]],
			   value:[0,0],
			   // arrResult: [],
			   // arrs: []
			}
		},
		onShow() {
			this.getDetails()
			this.dialog()
			console.log(!this.getList().length)
			if(!this.getList().length) {
				this.categoryList = [{id: 0,categoryName: '未归档'}]
				this.array[0].push(...this.categoryList)
			} else {
				this.categoryList = this.getList()
				this.array[0] = this.categoryList
			}
			if(this.getCardDataList().length) {
				this.cardDataList = this.getCardDataList()
			}
		},
	    methods: {
			// 获取剪贴板内容
			getDetails() {
			  const _this = this
			  console.log(_this)
			  uni.getClipboardData({
			      success: function (res) {
			          // console.log(res.data);
			  		if(res.data) {
			  			_this.details = res.data
						console.log(_this.details)
			  		}
			      }
			  })
			},
			// 获取本地缓存分类名称数组
			getList() {
			  try {
			       const value = JSON.parse(uni.getStorageSync('categoryList'));
			       if (value) {
			           return value
			      }
			   } catch (e) {
				  return []
			   }
			},
			// 获取本地缓存卡片数组
			getArray() {
			  try {
			       const value = JSON.parse(uni.getStorageSync('array'))
			       if (value) {
			           return value
			      }
			   } catch (e) {
				  return []
			   }
			},
			// 弹出剪贴板对话框
			dialog() {
				if(!this.details) {
					this.show = true
				}
			},
			// 剪贴板对话框q取消确定按钮
			onClick (index) {
				console.log(index.index)
				if (index.index === 0) {
					this.show = false
				} else {
					console.log('确定')
					this.cardData.content = this.details
					console.log(this.cardData.content)
					this.show = false
				}
				
			},
			// 保存失败对话框取消确定按钮
			onSave (index) {
				console.log(index.index)
				if (index.index === 0) {
					this.showSave = false
				} else {
					this.showSave = false
				}
				
			},
			// 关闭剪贴板对话框
			onClose() {
				this.show = false
			},
			// 关闭保存失败对话框
			onfailClose() {
				this.showSave = false
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
			},
			picker2(e){
			            console.log(e.detail.value)
			            this.value = e.detail.value
						// this.array[0][this.value[0]]
						// console.log(this.array[0][this.value[0]])
						// this.array[0][this.value[0]]
						// console.log(this.array[0][this.value[0]].id)
						this.cardData.cid = this.array[0][this.value[0]].id
						this.cardData.id = parseInt(Math.random() * 1000000000)
						// this.array[0][this.value[0]][this.cardData.title] = this.cardData
						this.cardDataList.push(this.cardData)
						console.log(this.cardDataList)
						console.log(this.cardData.content)
						if(this.cardData.content) {
							try {
							    uni.setStorageSync('cardDataList', JSON.stringify(this.cardDataList))
							} catch (e) {
							    // error
							}
							this.showToast()
						} else {
							console.log('保存卡片失败')
							this.showSave = true
						}
						
						
						this.cardData = {}
						console.log(this.array)
						
		    },
			save() {
				console.log(this.cardData)
			},
			//调用方法showToast显示提示信息
			showToast(type) {
					let options = {}
					this.$refs.toast.show(options)
				}
				  
	    }
	}
</script>

<style scoped>
	.add {
		background: #f5f5f5;
		height: 100vh;
	},
	.title {
		margin: 20rpx;
		
		text-align: center;
		font-size: 40rpx;
		font-weight: 400;
	}
	.content {
		overflow-y:scroll;
		height: 750rpx;
		margin: 0 20rpx;
	}
	.btn {
		margin: 30rpx 20rpx;
		
	}
</style>
