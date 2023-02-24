<template>
	<view class="w-full flex items-center pingfang p-2"
		style="height: 100rpx; background-color: #f4faff; justify-content: space-around">
		<view class="flex flex-col items-center" @tap="get_jump" :data-url="item" v-for="(item, index) in tabbar_list"
			:key="index">
			<image :src="item.icon" style="width: 19px; height: 20px"></image>

			<view class="text-center text-xs" style="padding-top: 10rpx"
				:style="select==index? 'color: #2d8cf0' : 'color: #999999'">{{ item.name }}</view>
		</view>
	</view>
</template>

<script>
	// custom-tab-bar/index.js
	export default {
		data() {
			return {
				tabbar_list: [{
						icon: '/static/images/icon/Home.png',
						selecticon: "",
						select: true,
						name: '首页',
						page_url: '../index/index'
					},
					{
						icon: "/static/images/icon/send.png",
						selecticon: "",
						select: false,
						name: "发布",
						page_url: "../send/index"
					},
					{
						icon: '/static/images/icon/Group.png',
						selecticon: "",
						select: false,
						name: '购物袋',
						page_url: '../shop_cart/index'
					},
					{
						icon: '/static/images/icon/username.png',
						selecticon: "",
						select: false,
						name: '我的',
						page_url: '../user/index'
					}
				]
			};
		},
		props: {
			select: {
				type: Number,
				default: 0
			}
		}
		/**
		 * 生命周期函数--监听页面加载
		 */
		,
		onLoad(options) {
			// wx.loadFontFace({
			//   family: 'PingFang',
			//   source: 'url("https://636c-cloud1-1g1p0gb13cd58cde-1316153423.tcb.qcloud.la/%E8%8B%B9%E6%96%B9-%E7%AE%80.ttf?sign=0737c1b101f64545d231a285fcc1b796&t=1672448538")',
			//   success(){
			//     console.log('字体下载成功')
			//   }
			// })
		},
		/**
		 * 生命周期函数--监听页面初次渲染完成
		 */
		onReady() {},
		/**
		 * 生命周期函数--监听页面显示
		 */
		onShow() {},
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
			get_jump(url) {
				let tabbar = this.tabbar_list.map(item => {
					item.select = false
					if (item.page_url == url.currentTarget.dataset.url.page_url) {
						item.select = true
						uni.switchTab({
							url: url.currentTarget.dataset.url.page_url,
						});
					}
					return item
				})

				this.tabbar_list = tabbar
			}
		}
	};
</script>
<style>
	/* custom-tab-bar/index.wxss */
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
</style>
