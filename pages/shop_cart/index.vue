<template>
	<view style="height: 100%">
		<view class="flex p-2 justify-center items-center pingfang rounded-xl mx-auto"
			style="background: #3787ff; width: 80%; margin-top: 10rpx">
			<image style="width: 24px; height: 24px" src="/static/images/icon/Iconly_Light_Notification.png"></image>

			<view class="text-white" style="padding-left: 20rpx; font-size: 12px">付款后，24小时内我们发货</view>
		</view>

		<view class="flex shadow-xl p-2 mx-auto rounded-xl pingfang" v-show="item.show"
			style="background-color: white; width: 85%; margin-top: 10px;;justify-content: space-between"
			v-for="(item, index) in shop_cart" :key="index">
			<image mode="aspectFill" style="width: 200rpx; height: 200rpx" :src="item.img[0]"></image>

			<view class="" style="padding: 5px">
				<view style="font-size: 12px">{{ item.name }}.</view>
				<view class="font-blod" style="padding-top: 50rpx">${{item.price * item.count}}</view>
			</view>

			<view class="flex item-end">
				<view @click="sub(item,index)" class="flex items-center justify-center p-1 rounded-xl"
					style="background-color: rgb(208, 209, 211); width: 40rpx">-</view>
				<view class="p-1 flex justify-center items-center" style="width: 40rpx">{{item.count}}</view>
				<view @click="add(index)" class="flex text-white items-center justify-center p-1 rounded-xl"
					style="background-color: rgb(29, 143, 236); width: 40rpx">+</view>
			</view>
		</view>

		<view class="fixed w-full bottom-'0' flex p-2 items-center justify-between pingfang"
			style="height: 110rpx; background-color: white; bottom: 131rpx">
			<view style="font-size: 14px">
				<view>共计</view>
				<view class="font-blod" style="padding-top: 10rpx">￥ {{ user.sum }}</view>
			</view>

			<view class="p-2 text-center rounded-xl text-white"
				style="width: 200rpx; background-color: rgb(17, 107, 226); margin-right: 30rpx; font-size: 12px"
				@tap="get_shop_jiesuan">
				去结账
			</view>
		</view>
		<customTabBar style="position: fixed;bottom: 0px;width: 100%;" select="2"></customTabBar>
	</view>
</template>

<script>
	const app = getApp()
	// pages/shop_cart/index.js
	import customTabBar from "../../custom-tab-bar/index.vue";
	export default {
		components: {
			customTabBar
		},
		data() {
			return {
				list: [],
				shop_cart: [],
				baseurl: app.globalData.BaseUrl,
				user: {
					sum: 0
				}
			};
		}
		/**
		 * 生命周期函数--监听页面加载
		 */
		,
		onLoad(options) {
			// uni.loadFontFace({
			// 	family: 'PingFang',
			// 	source: 'url("https://636c-cloud1-1g1p0gb13cd58cde-1316153423.tcb.qcloud.la/%E8%8B%B9%E6%96%B9-%E7%AE%80.ttf?sign=0737c1b101f64545d231a285fcc1b796&t=1672448538")',
			// 	success() {
			// 		console.log('字体下载成功');
			// 	}
			// });
		},
		/**
		 * 生命周期函数--监听页面初次渲染完成
		 */
		onReady() {

		},
		/**
		 * 生命周期函数--监听页面显示
		 */
		onShow() {
			let token = uni.getStorageSync('token') || false
			if (token) {
				this.$data.list = JSON.parse(uni.getStorageSync('cart')) || []
				let that = this
				uni.request({
					url: this.$data.baseurl + 'getShopCart',
					method: 'POST',
					data: {
						goods: this.$data.list
					},
					success: (res) => {
						that.$data.shop_cart = []
						that.$data.user.sum = 0
						that.$data.shop_cart.push(...res.data.data)
						console.log('that.$data.shop_cart', that.$data.shop_cart)

						that.$data.shop_cart.forEach(item => {
							if (item.show) {
								that.$data.user.sum += item.price * item.count
							} else {

							}
						})

					}
				})

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
		onHide() {
			let that = this
			console.log('隐藏了')
		},
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
			get_shop_jiesuan() {
				uni.navigateTo({
					//加密 防止 传递参数太长
					url: `../get_shop_jiesuan/index?arr=` + encodeURIComponent(JSON.stringify(this.$data
						.shop_cart))
				});
				console.log('shop_cart', this.$data.shop_cart)
			},
			add(index) {
				this.shop_cart[index].count++
				let arr = JSON.parse(uni.getStorageSync('cart'))
				arr[index].count++
				uni.setStorageSync('cart', JSON.stringify(arr))
				this.user.sum += this.shop_cart[index].price

			},
			sub(item, index) {
				console.log(item)
				if (this.list[index].count > 1) {
					this.shop_cart[index].count--
					this.$data.list[index].count--
					let arr = JSON.parse(uni.getStorageSync('cart'))
					arr[index].count--
					uni.setStorageSync('cart', JSON.stringify(arr))
					this.user.sum -= this.shop_cart[index].price
				} else {
					let good_id = item.good_id
					this.$data.user.sum -= item.price
					this.shop_cart.splice(index, 1)
					let arr = JSON.parse(uni.getStorageSync('cart'))
					arr.forEach((item, i) => {
						if (item.id == good_id) {
							arr.splice(i, 1)
						}
					})

					uni.setStorageSync('cart', JSON.stringify(arr))
				}
			}
		}
	};
</script>
<style>
	/* pages/shop_cart/index.wxss */
	.flex {
		display: flex;
	}

	.border {
		border: 1px solid rgba(0, 0, 0, 0.3);
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
		box-shadow: 1px 9px 50px -12px rgba(0, 0, 0, 0.25);
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
