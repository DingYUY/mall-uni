<template>
	<view style="height: 100%">
		<view class="flex justify-between items-center" style="margin-top: 79rpx; padding: 10px">
			<view class="" style="font-weight: 900; font-size: 12px; color: rgb(102, 102, 102)">
				<view style="font-size: 14px; color: black; width: 444rpx">Welcome to Second hand trading platform
				</view>
				<view style="margin-top: 10rpx">欢迎使用二手交易平台</view>
			</view>

			<view class="pingfang border text-center flex justify-center items-center"
				style="padding: 4px; height: 20px; border-radius: 6px; font-size: 12px; color: rgb(136, 6, 136)"
				@click="gohome">
				<image src="/static/images/icon/编辑.png" style="width: 16px; height: 16px; margin-right: 7px"></image>
				发布
			</view>
		</view>

		<swiper autoplay indicator-dots style="margin-top: 20rpx; width: 100%; margin: '0' auto">
			<swiper-item class="obj-cover">
				<image mode="aspectFill" class="w-full obj-cover"
					src="https://gimg2.baidu.com/image_search/src=http%3A%2F%2Fc-ssl.duitang.com%2Fuploads%2Fblog%2F202105%2F09%2F20210509012315_89541.thumb.1000_0.jpeg&refer=http%3A%2F%2Fc-ssl.duitang.com&app=2002&size=f9999,10000&q=a80&n=0&g=0n&fmt=auto?sec=1676546432&t=5b369e0633251f3e3054b67972d4baa2">
				</image>
			</swiper-item>
		</swiper>

		<view class="pingfang text-xs flex justify-between items-center mx-auto" style="width: 90%; margin-top: 20rpx">
			<view class="pingfang p-2 text-center border-b-2" style="font-size: 14px">热门商品</view>

		</view>

		<view class="wrapper" style="width: 100%;">
			<view class="scroll-content" style="overflow: scroll;">
				<view class="shadow-xl rounded-xl" @tap="get_shop_deatiled" :data-id="item.good_id" v-if="item.show"
					style="width: 400rpx; display: inline-block; margin: 10px" v-for="(item, index) in good_list"
					:key="index">
					<image style="width: 100%; height: 90px" mode="aspectFill" :src="item.img[0]"></image>
							<view style="padding-left: 20rpx;">{{ item.name }}</view>
					<view class="pingfang font-bold flex justify-between"
						style="padding-left: 10px; padding-right: 10px; padding-top: 10px;    align-items: center;">
						<view class="pingfang font-bold flex justify-between"
							style="padding-left: 10px; padding-right: 10px;">
							<image src="/static/images/icon/Path.png" style="width: 12px; height: 15px;transform: translate(-20rpx,0px);"></image>
						</view>

					<view class="flex items-center" style="padding: 10px">
							<image :src="item.user_img" style="width: 40px;height: 40px;border-radius: 40px"></image>
							<view style="font-size: 12px;padding-left: 10px;width: 51rpx;" class="text-ellipsis">{{item.username}}</view>
						</view>

						<view style="font-size: 12px;" class="pingfang">
							￥{{item.price}}</view>

					</view>
				</view>
			</view>



			<view class="pingfang text-xs flex justify-between items-center mx-auto"
				style="width: 90%; margin-top: 20rpx">
				<view class="pingfang p-2 text-center border-b-2" style="font-size: 14px">所有商品</view>
			</view>


			<view class="w-full pingfang flex" @tap="get_shop_deatiled" v-if="item.show" :data-id="item.good_id"
				style="margin-top: 10px;background: white;padding: 10px;height: 240px" v-for="(item,index) in good_list"
				:key="index">
				<image :src="item.img[0]" style="width: 40%" mode="aspectFill"></image>

				<view class="flex  flex-col " style="height: 100%;width: 60%;justify-content: space-between">

					<view style="padding: 10px" class="flex justify-between">

						<view>
							<view style="font-weight: 900">{{item.name}}</view>
							<view style="padding-top: 10px">￥{{item.price}}</view>
						</view>

						<view class="flex items-center" style="padding: 10px">
							<image :src="item.user_img" style="width: 40px;height: 40px;border-radius: 40px"></image>
							<!--                <view style="font-size: 12px;padding-left: 10px;">{{item.username}}</view>-->
						</view>

					</view>


					<view class="text-xs" style="width: 60%;overflow: hidden;text-overflow: ellipsis;padding: 10px;">
						{{item.introduce}}
					</view>


				</view>
			</view>

		</view>
		<customTabBar style="position: fixed;bottom: 0px;width: 100%;" select="0"></customTabBar>
	</view>
	
	
</template>

<script>
	// index.js
	// 获取应用实例
	import customTabBar from "../../custom-tab-bar/index.vue";
	const app = getApp();
	export default {
		components:{
				customTabBar
		},
		data() {
			return {
				motto: 'Hello World',
				userInfo: {},
				hasUserInfo: false,
				canIUse: uni.canIUse('button.open-type.getUserInfo'),
				canIUseGetUserProfile: false,
				canIUseOpenData: uni.canIUse('open-data.type.userAvatarUrl') && uni.canIUse(
					'open-data.type.userNickName'), // 如需尝试获取用户信息可改为false
				BaseUrl: app.globalData.BaseUrl,
				page: 1,
				limit: 15,
				good_list: []
			};
		},
		onLoad() {

		},
		//触底加载
		onReachBottom() {
			let that = this;
			uni.request({
				url: this.$data.BaseUrl + 'getOrderPage',
				method: "POST",
				data: {
					page: this.$data.page,
					limit: this.$data.limit
				},
				success(res) {
					console.log(res.data)
					if (res.data.code == 1) {
						//添加
						that.$data.good_list.push(...res.data.data)
						that.$data.page++;
					}
				}
			})
		},
		onShow() {
			let that = this
			this.page = 1
			this.good_list.length = 0
			uni.request({
				url: this.$data.BaseUrl + 'getOrderPage',
				method: "POST",
				data: {
					page: that.$data.page,
					limit: this.$data.limit
				},
				success(res) {
					console.log(res.data)
					if (res.data.code == 1) {
						//添加
						that.$data.good_list.push(...res.data.data)
						that.$data.page++;
					}
				}
			})
		},

		methods: {
			// 事件处理函数
			bindViewTap() {
				uni.navigateTo({
					url: '../logs/logs'
				});
			},

			getUserProfile(e) {
				// 推荐使用wx.getUserProfile获取用户信息，开发者每次通过该接口获取用户个人信息均需用户确认，开发者妥善保管用户快速填写的头像昵称，避免重复弹窗
				uni.getUserProfile({
					desc: '展示用户信息',
					// 声明获取用户个人信息后的用途，后续会展示在弹窗中，请谨慎填写
					success: (res) => {
						console.log(res);
						this.setData({
							userInfo: res.userInfo,
							hasUserInfo: true
						});
					}
				});
			},

			getUserInfo(e) {
				// 不推荐使用getUserInfo获取用户信息，预计自2021年4月13日起，getUserInfo将不再弹出弹窗，并直接返回匿名的用户个人信息
				console.log(e);
				this.setData({
					userInfo: e.detail.userInfo,
					hasUserInfo: true
				});
			},

			get_shop_deatiled(id) {
				uni.navigateTo({
					url: "/pages/shop_deailed/index?id=" + id.currentTarget.dataset.id
				})
			},

			gohome() {
				uni.switchTab({
					url: "/pages/send/index"
				})
			}
		}
	};
</script>
<style>
	page {
		background: #f5f5f5;
	}

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

	.wrapper {
		/* overflow-x: scroll; */
		/* overflow-y: hidden; */
	}

	.scroll-content {
		white-space: nowrap;
	}

	.item {
		display: inline-block;
		width: 200px;
	}
	
	  /*超出文本自动省略*/
	    .text-ellipsis {
	        overflow: hidden;
	        text-overflow: ellipsis;
	        white-space: nowrap;
	    }

</style>
