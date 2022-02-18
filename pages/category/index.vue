<template>
	<view class="category-container">
		<view class="head" >
			<button class="btn" type="primary" plain size="mini" @click="open">+添加分类</button>
			</view>
		<view class="card-container" >
			<view class="item" v-for="(item, index) in dataList" :key="index" @longpress="showPopup(index)" @click="showCardBox(index, item.children)" >
				<view class="add">{{item.categoryName}}</view>
				<view class="card-bottom">{{ item.children ? item.children.length : '0'}}</view>
			</view>
			<view class="item" @click="open">
				<view class="add">新建分类</view>
				<view class="card-bottom-bulid">+</view>
			</view>
			
		</view>
		<!-- 添加分类弹出框 -->
		<uni-popup ref="popup" type="dialog">
		    <uni-popup-dialog ref="sss" title="请输入分类名称" placeholder=" " mode="input" message="成功消息" :duration="2000" :before-close="true" @close="close" @confirm="confirm"></uni-popup-dialog>
		</uni-popup>
		<!-- 底部弹出框 -->
		<fui-bottom-popup :show="show" @close="closePopup" zIndex="9999999" background="#2e2e2e">
			<view class="fui-custom__wrap">
				<!-- <view class="pigeonhole">
					<fui-button  background="#777" color="#fff" radius="0">移动到未归档</fui-button>
				</view> -->
				<view class="pigeonhole">
					<fui-button background="#777" color="#fff" radius="0" @click="deleteCategory">删除该分类</fui-button>
				</view>
				<view class="cancel">
					<fui-button background="#777" color="#fff" radius="0" @click="cancelSelect">取消</fui-button>
				</view>		
			</view>
		</fui-bottom-popup>
		<!-- 不能删除默认分类轻提示 -->
		<fui-toast ref="toast"></fui-toast>
		<!-- 该分类下没有卡片对话框 -->
		<fui-dialog :show="shownNocard" :content="pp" maskClosable @click="onClick" @close="onClose"></fui-dialog>
	</view>
 
</template>

<script>
	export default {
		data(){
			return{
			  show: false,
			  index: '',
			  shownNocard: false,
			  pp: '里面没有卡片',
			  categoryList:[{id: 0,categoryName: '未归档'}],
			  cardDataList: [],
			  dataList: []
			}
		},
		onShow() {
			if(this.getList().length) {
				console.log(this.getList())
				this.categoryList = this.getList()
			}
			this.cardDataList = this.getCardDataList()
			console.log(this.cardDataList)
			const list = [...this.categoryList,...this.cardDataList]
			console.log(list)
			this.dataList = this.toTree(list)
			this.savaDataList()
			console.log(this.dataList)
			console.log(this.categoryList)
		},
	    methods: {
			getList() {
			  try {
			       const value = JSON.parse(uni.getStorageSync('categoryList'))
			       if (value) {
			           return value
			      }
			   } catch (e) {
				  return []
			   }
			},
	        open() {
	            this.$refs.popup.open()
	        },
	        /**
	         * 点击取消按钮触发
	         * @param {Object} done
	         */
	        close() {
	            // TODO 做一些其他的事情，before-close 为true的情况下，手动执行 close 才会关闭对话框
	            // ...
				// 清空输入框的值
				this.$refs.sss.$data.val = ''
	            this.$refs.popup.close()
	        },
	        /**
	         * 点击确认按钮触发
	         * @param {Object} done
	         * @param {Object} value
	         */
	        confirm(value) {
	            // 输入框的值
	            console.log(value)
				console.log(this.$refs.sss.$data.val)
				console.log(this.categoryList)
				this.categoryList.push({id: this.categoryList.length , categoryName: value})
				console.log(this.categoryList)
				try {
				    uni.setStorageSync('categoryList', JSON.stringify(this.categoryList))
				} catch (e) {
				    // error
				}
				// sss
				const list = [...this.categoryList,...this.cardDataList]
				console.log(list)
				this.dataList = this.toTree(list)
	            // 清空输入框的值
				this.$refs.sss.$data.val = ''
	            this.$refs.popup.close()
	        },
			//调用此方法显示底部弹出层
				showPopup(index) {
					this.index = index
					console.log(this.index)
					if (this.index === 0 ) {
						this.showToast()
					} else {
						this.show = true
					}
				},
				closePopup(type) {
					this.index = ''
					this.show = false
				},
		    // 删除分类
			deleteCategory() {
				this.dataList.splice(this.index, 1)
				this.categoryList.splice(this.index, 1)
				// 删除对应卡片数据，并保存
				this.cardDataList.splice(this.index, 1)
				try {
				    uni.setStorageSync('cardDataList', JSON.stringify(this.cardDataList))
				} catch (e) {
				    // error
				}
				try {
				    uni.setStorageSync('categoryList', JSON.stringify(this.categoryList))
				} catch (e) {
				    // error
				}
				this.closePopup()
			},
			cancelSelect() {
				this.closePopup()
			},
			// //调用方法showToast显示提示信息
			showToast(type) {
					let options = {}
					//提示信息
					options.text = '默认分类无法删除';
					this.$refs.toast.show(options)
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
				showCardBox(index, children) {
					console.log(index, children)
					if(children === undefined) {
						this.shownNocard = true
						console.log('跳出弹框，提醒该分类下没有卡片')
					} 
					else {
						uni.navigateTo({
						    url: '../cardBox/index?index=' + index
						})
					}
				},
				// 转换为树形数据
				toTree(data) {
				        // 删除 所有 children,以防止多次调用
				        data.forEach(function (item) {
				            delete item.children;
				        });
				 
				        // 将数据存储为 以 id 为 KEY 的 map 索引数据列
				        var map = {};
				        data.forEach(function (item) {
				            map[item.id] = item;
				        });
				//        console.log(map);
				        var val = [];
				        data.forEach(function (item) {
				            // 以当前遍历项，的pid,去map对象中找到索引的id
				            var parent = map[item.cid];
				            // 好绕啊，如果找到索引，那么说明此项不在顶级当中,那么需要把此项添加到，他对应的父级中
				            if (parent) {
				                (parent.children || ( parent.children = [] )).push(item);
				            } else {
				                //如果没有在map中找到对应的索引ID,那么直接把 当前的item添加到 val结果集中，作为顶级
				                val.push(item);
				            }
				        });
						console.log(val)
				        return val;
				    },
					// 保存dataList
					savaDataList() {
						console.log(this.dataList)
						try {
						    uni.setStorageSync('dataList', JSON.stringify(this.dataList))
						} catch (e) {
						    // error
						}
					},
					// 关闭没有卡片对话框
					onClose() {
						this.shownNocard = false
					},
					// 没有卡片对话框取消确定按钮
					onClick (index) {
						console.log(index.index)
						if (index.index === 0) {
							this.shownNocard = false
						} else {
							this.shownNocard = false
						}
						
					},
				
				
	
				  
	    }
	}
</script>

<style scoped>
	.category-container {
		background: #E5E7EB;
		height: 100vh;
		overflow-y: auto; 
	}
	.category-container .head {
		text-align: right;
		padding-right:20rpx ;
		font-size: 40rpx;
		height: 80rpx;
		line-height: 70rpx;
		/* background: #fff; */
	}
	.btn {
		margin-top: 10rpx;
	}
	.card-bottom {
		color: #000;
		font-size: 80rpx;
		text-align: center;
		height: 240rpx;
		line-height: 240rpx;
	}
	.card-bottom-bulid {
		color:#000;
		text-align: center;
		font-size: 80rpx;
		height: 240rpx;
		line-height: 240rpx;
	}
	 .card-container {
		display: flex;
		flex-wrap: wrap;
		justify-content: space-between;
	}
	.item {
		width: 340rpx;
		height: 340rpx;
		margin: 10rpx 10rpx;
		border-radius: 15rpx;
		box-sizing: border-box;
		background: #fff;
		/* display: flex; */
		/* flex-direction: column; */
		/* justify-content: center; */
		/* align-items: center; */
			color: #D1D5DB;
			font-size: 50rpx;
			box-shadow: 0px 0px 8px 0px rgb(43 145 255 / 58%)
	}
	.add {
		font-size: 45rpx;
		color: #fff;;
		height: 100rpx;
		line-height: 100rpx;
		text-align: center;
		background: #1ab16d;
		border-radius: 15rpx 15rpx 0 0 ;
	}
	.pigeonhole {
		border-bottom: 1px solid #DCDCDC;
	}
	.cancel {
		margin-top: 15rpx;
	}
</style>
