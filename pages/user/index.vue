<template>
	<view style="height: 100%">
		<!-- 头像部分 -->
		<view class="flex items-center flex-col pingfang" style="margin-top: 70rpx">
			<image style="width: 100px; height: 100px" class="rounded-full" mode="aspectFill" :src="user.head_img">
			</image>
			<view style="margin-top: 20rpx">{{ user.username }}</view>
			<view class="yu_e_card flex items-center justify-center"  @tap="goMeGood">
				<view class="flex flex-col justify-center items-center " style="width: 50%; height: 60%">
					<view class="text-xs" style="margin-bottom: 20rpx; color: #e2e1e1fa">我的商品</view>
					<view class="text-white font-blod">{{user.good_length}}</view>
				</view>
			</view>


		</view>

		<!-- 更多功能 -->
		<view class="pingfang font-blod" style="font-size: 14px; padding: 60rpx">更多功能</view>

		<view class="flex flex-col pingfang w-full" style="margin-top: 30rpx" @click.self="go_good">
			<view class="border rounded-xl mx-auto flex items-center"
				style="width: 80%; height: 150rpx; padding: 5px; padding-left: 20px">
				<image src="/static/images/icon/Security.png" style="width: 38px; height: 38px"></image>
				<view style="padding-left: 20rpx">
					<view class="" style="font-size: 12px">发布商品</view>
					<view style="color: rgb(102, 102, 102)" class="text-xs">发布商品，衣服 书等物品 </view>
				</view>
			</view>
		</view>

		<view class="flex flex-col pingfang" style="margin-top: 30rpx">
			<view class="border rounded-xl mx-auto flex items-center" @click="go_dingdan()"
				style="width: 80%; height: 150rpx; padding: 5px; padding-left: 20px">
				<image src="/static/images/icon/help.png" style="width: 38px; height: 38px"></image>
				<view style="padding-left: 20rpx">
					<view class="" style="font-size: 12px">订单管理</view>
					<view style="color: rgb(102, 102, 102)" class="text-xs">进行订单的管理，收货发货 等操作</view>
				</view>
			</view>

		</view>
		<view class="flex flex-col pingfang" style="margin-top: 30rpx">
			<view class="border rounded-xl mx-auto flex items-center" @click="go_address()"
				style="width: 80%; height: 150rpx; padding: 5px; padding-left: 20px">
				<image src="/static/images/icon/help.png" style="width: 38px; height: 38px"></image>
				<view style="padding-left: 20rpx">
					<view class="" style="font-size: 12px">地址管理</view>
					<view style="color: rgb(102, 102, 102)" class="text-xs">进行地址的管理，添加地址 等操作</view>
				</view>
			</view>
		</view>

		<view @click="logout"
			style="margin-bottom: 0px;border-radius: 9px;width: 20%;background: #007aff;height: 40px;color: white;text-align: center;margin-top: 20px"
			class="pingfang flex justify-center items-center mx-auto ">登出</view>

		<view style="height: 100px"></view>
		<customTabBar style="position: fixed;bottom: 0px;width: 100%;" select="3"></customTabBar>
	</view>
</template>

<script>
	const app = getApp()
	// pages/user/index.js
	import customTabBar from "../../custom-tab-bar/index.vue";
	export default {
		components:{
				customTabBar
		},
		data() {
			return {
				user: {
					head_img: '',
					username: uni.getStorageSync('username') || '未登录',
					baseurl: app.globalData.BaseUrl,
					good_length: 0
				}
			};
		}
		/**
		 * 生命周期函数--监听页面加载
		 */
		,
		onLoad(options) {
			uni.loadFontFace({
				family: 'PingFang',
				source: 'url("https://636c-cloud1-1g1p0gb13cd58cde-1316153423.tcb.qcloud.la/%E8%8B%B9%E6%96%B9-%E7%AE%80.ttf?sign=0737c1b101f64545d231a285fcc1b796&t=1672448538")',
				success() {
					console.log('字体下载成功');
				}
			});
		},

		mounted() {
			let that = this
			uni.request({
				url: 'http://127.0.0.1:3175/getMyGoods',
				method: "POST",
				data: {
					user_id: uni.getStorageSync('id')
				},
				success(res) {
					console.log(res)
					if (res.data.code == 1) {
						that.$data.user.good_length = res.data.data.length
					}
				}
			})
		},

		/**
		 * 生命周期函数--监听页面初次渲染完成
		 */
		onReady() {},
		/**
		 * 生命周期函数--监听页面显示
		 */
		onShow() {
			let token = uni.getStorageSync('token') || false
			if (token) {
				console.log('有token')
				this.login()
			} else { //没有token
				console.log('没有token')
				uni.navigateTo({
					url: "/pages/login/index"
				})
			}
		},
		/**
		 * 生命周期函数--监听页面隐藏
		 */
		onHide() {},
		/**
		 * 生命周期函数--监听页面卸载
		 */
		onUnload() {},
		/**
		 * 页面相关事件处理函数--监听用户下拉动作
		 */
		onPullDownRefresh() {},
		/**
		 * 页面上拉触底事件的处理函数
		 */
		onReachBottom() {},
		/**
		 * 用户点击右上角分享
		 */
		onShareAppMessage() {},
		methods: {
			logout() {
				uni.removeStorageSync('token')
				uni.removeStorageSync('username')
				uni.removeStorage('id')
				uni.removeStorageSync('user_img')
				uni.removeStorageSync('cart')
				uni.showToast({
					title: '登出成功',
					icon: 'none'
				})
				setTimeout(() => {
					uni.reLaunch({
						url: '/pages/login/index'
					})
				}, 1000)
			},
			go_good() {
				uni.switchTab({
					url: "/pages/send/index"
				})
			},

			go_dingdan() {
				uni.navigateTo({
					url: "/pages/dingdan/index"
				})
			},
			
			go_address() {
				uni.navigateTo({
					url: "/pages/address/index"
				})
			},

			// 利用登录函数更新头像
			login() {
				let that = this;
				uni.request({
					url: 'http://127.0.0.1:3175/' + 'getUserHead',
					method: 'POST',
					data: {
						_id: uni.getStorageSync('id')
					},
					success(res) {
						if (res.data.code == 1) {
							uni.setStorageSync('user_img', res.data.data.img_head);
							uni.setStorageSync('token', res.data.data.password);
							uni.setStorageSync('id', res.data.data._id)
							uni.setStorageSync('username', res.data.data.name)
							that.$data.user.head_img = uni.getStorageSync('user_img', res.data.data.img_head);
						} else {

						}
					}
				})
			},
			
			
			// 跳转到我的商品
			goMeGood(){
				uni.navigateTo({
					url:"../meGood/index"
				})
			}
			
		}
	};
</script>
<style>
	/* pages/user/index.wxss */

	.yu_e_card {
		width: 80%;
		height: 170rpx;
		background-color: #3787ff;
		padding: 0 20rpx;
		box-sizing: border-box;
		border-radius: 20rpx;
		margin: 0 auto;
		margin-top: 30rpx;
	}

	.yu_e_left {
		border-right: 1rpx solid #fff;
	}

	/* pages/user/index.wxss */

	.yu_e_card {
		width: 80%;
		height: 170rpx;
		background-color: #3787FF;
		padding: 0 20rpx;
		box-sizing: border-box;
		border-radius: 20rpx;
		margin: 0 auto;
		margin-top: 30rpx;
	}


	.yu_e_left {
		border-right: 1rpx solid #fff;
	}

	/* custom-tab-bar/index.wxss */
	.flex {
		display: flex;
	}

	.border {
		border: 1px solid rgba(0, 0, 0, .3)
	}


	.flex-col {
		flex-direction: column;
	}

	.text-center {
		text-align: center;
	}

	.p-1 {
		padding: 0.25rem;
	}

	.p-2 {
		padding: 0.5rem;
	}


	.text-xs {
		font-size: 9px;
	}

	.justify-center {
		justify-content: center;
	}

	.justify-between {
		justify-content: space-between;
	}

	.justify-end {
		justify-content: flex-end;
	}

	.obj-cover {
		object-fit: cover;
	}

	.obj-contain {
		object-fit: contain;
	}

	.w-full {
		width: 100%;
	}

	.items-center {
		align-items: center;
	}

	.item-end {
		align-items: flex-end;
	}

	.pingfang {
		font-family: PingFang;
	}

	.rounded-xl {
		border-radius: 0.5rem;
	}

	.rounded-full {
		border-radius: 99%;
	}

	.border-b-2 {
		border-bottom-right-radius: 2px;
		border-bottom-left-radius: 2px;
	}

	.mx-auto {
		margin-left: auto;
		margin-right: auto;
	}


	.shadow-xl {
		box-shadow: 1px 9px 50px -12px rgba(0, 0, 0, 0.25)
	}

	.font-blod {
		font-weight: bold;
	}

	.text-white {
		color: white;
	}

	.absolute {
		position: absolute;
	}


	.fixed {
		position: fixed;
	}

	.bottom-0 {
		bottom: 0px;
	}

	.wrapper {
		overflow-x: scroll;
		overflow-y: hidden;
	}

	.scroll-content {
		white-space: nowrap;
	}

	.item {
		display: inline-block;
		width: 200px;
	}
</style>
