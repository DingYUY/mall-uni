<template>
	<view style="height: 100%">
		<view class="absolute flex text-white items-center justify-between" style="z-index: 1; width: 100%; top: 69rpx">
			<image src="/static/images/icon/Iconly_Light_Arrow - Left.png" @tap="back"
				style="width: 32px; height: 36px; padding-left: 10px"></image>
			<!-- <image @click="joinCart" src="/static/images/icon/Path_false.png"
				style="width: 20px; height: 24px; padding: 30px"></image> -->
		</view>

		<swiper style="height: 254px" indicator-dots indicator-active-color="rgba(255,255,255,.9)" autoplay="">
			<swiper-item v-for="(item, index) in swieprList" :key="index">
				<image mode="aspectFill" class="w-full" :src="item"></image>
			</swiper-item>
		</swiper>

		<view class="flex" style="justify-content: flex-end; padding: 10px">
			<view style="background-color: white; width: 60px; justify-content: space-around"
				class="border p-2 rounded-xl flex items-center pingfang">
				<image src="/static/images/icon/star.png" style="width: 11px; height: 11px"></image>
				<view>4.9</view>
			</view>
		</view>
		<view class="pingfang" style="font-weight: 900; padding: 10px; margin-top: 40rpx">商品详情</view>

		<view class="pingfang p-2" style="font-size: 12px; color: gray">
			{{list[0].introduce}}
		</view>

		<view class="pingfang font-blod flex justify-between" style="padding: 20rpx">
			<view>价格</view>
			<view>￥{{list[0].price}}</view>
		</view>

		<view @click="go_jiesuan" class="flex items-center justify-center rounded-xl text-white mx-auto"
			style="width: 250rpx; background: rgb(30, 81, 190); padding: 20rpx; margin-top: 20rpx; margin-bottom: 30rpx">
			立即购买
		</view>



		<view class="flex items-center justify-center rounded-xl text-white mx-auto"
			style="width: 250rpx; background: rgb(30, 81, 190); padding: 20rpx; margin-top: 20rpx; margin-bottom: 30rpx"
			@click="joinCart">
			加入购物车
		</view>


	</view>
</template>

<script>
	const app = getApp();
	// pages/shop_deailed/index.js
	export default {
		data() {
			return {
				id: '',
				BaseUrl: app.globalData.BaseUrl,
				swieprList: [],
				list: []

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
			let that = this;

			uni.request({
				url: this.$data.BaseUrl + 'getGoodsById',
				method: 'POST',
				data: {
					goods_id: options.id
				},
				success: (res) => {
					console.log(res.data.data[0])
					that.$data.swieprList.push(...res.data.data[0].img)
					that.$data.list.push(res.data.data[0])
				},
				fail: (err) => {
					console.log(err)
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
			back() {
				uni.navigateBack();
			},
			go_jiesuan() {

				//判断是否登录
				let token = uni.getStorageSync('id');
				if (token) {
					//生成一个数组包含对象 [{good_id:good_id,count:1}]
					this.$data.list[0].count = 1
					let arr = this.$data.list.map((item) => {
						console.log('item', item)
						return {
							good_id: item.good_id,
							count: item.count,
							price: item.price,
							img: item.img[0],
							introduce: item.introduce,
							address: item.address,
							name: item.name
						}
					})

					uni.navigateTo({
						url: '/pages/get_shop_jiesuan/index?arr=' + JSON.stringify(arr)
					})
				} else {
					uni.showToast({
						title: '请先登录',
						icon: 'none',
						duration: 2000
					});
					setTimeout(() => {
						uni.navigateTo({
							url: '/pages/login/index'
						});
					}, 2000)
				}
			},

			//加入购物车 存储本地

			joinCart() {
				let cart = uni.getStorageSync("cart");
				uni.showToast({
					title: "添加成功",
					icon: "success"
				})

				if (cart) {
					// Cart exists
					cart = JSON.parse(cart);
					// Check if product already in cart
					let index = cart.findIndex(item => item.id == this.$data.list[0].good_id);
					if (index != -1) {
						// Product already in cart
						cart[index].count++;
					} else {
						// Product not in cart
						cart.push({
							id: this.$data.list[0].good_id,
							count: 1
						});
					}
					uni.setStorageSync("cart", JSON.stringify(cart));
				} else {
					// No existing cart
					uni.setStorageSync("cart", JSON.stringify([{
						id: this.$data.list[0].good_id,
						count: 1
					}]));
				}
			}


		}
	};
</script>
<style>
	/* pages/shop_deailed/index.wxss */
	page {
		background-color: #fafafa;
	}

	.shop_card {
		background-color: #fff;
		margin: 0 10rpx;
		margin-top: 10rpx;
		padding: 10rpx;
		border-radius: 5rpx;
		box-shadow: 0 0 5rpx #ccc;
		border-radius: 10px;
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
