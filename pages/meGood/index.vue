<template>
	<view>
		<view v-for="(item,index) in list" :key="index" style="width: 95%;margin: 0 auto;margin-top: 10px;display: flex;  box-shadow: 0px 8px 15px rgba(0, 0, 0, 0.1);
">
			<image :src="item.img[0]" style="width: 40%" mode="aspectFill"></image>
			<view class="pingfang"
				style="width: 60%;display: flex;flex-direction: column;justify-content:space-between;padding: 10px;">
				<view class="" style="padding: 10px;">
					<view class="">商品名称：{{item.name}}</view>
					<view class="" style="padding-top: 20px;font-size: 12px;">简介：{{item.introduce}}</view>
					<view class="" style="padding-top: 20px;font-size: 12px;">发货地址：{{ item.address }}</view>
					<!-- 			<view class="" v-if="item.status==2" style="font-size: 12px;margin-top: 30px;">订单状态:<span style="color: dimgray;">订单完成</span></view>
				<view class="" v-if="item.status==3" style="font-size: 12px;margin-top: 30px;">订单状态:<span style="color: dimgray;">订单完成</span></view>
				<view class="" v-if="item.status==0" style="font-size: 12px;margin-top: 30px;">订单状态:<span style="color: dimgray;">未发货</span></view>
				<view class="" v-if="item.status==1" style="font-size: 12px;margin-top: 30px;">订单状态:<span style="color: dimgray;">发货中</span></view> -->
				</view>

				<view style="display: flex;">
					<view>￥{{item.price}}</view>
					<!-- 			<button class="button"  @click="editOrder(item._id,1,index)" v-if="item.status==0" style="vertical-align:middle;height: 20px;"><span>发货</span></button>
			<button class="button"  @click="editOrder(item._id,2,index)" :disabled="id!=item.user_id" v-if="item.status==1" style="vertical-align:middle;height: 20px;"><span>收货</span></button>
			<button class="button" :disabled="id!=item.user_id"  v-if="item.status==2" style="vertical-align:middle;height: 20px;"><span>订单完成</span></button> -->
				</view>



			</view>
		</view>
	</view>
</template>

<script>
	import van_card from '../../static/dist/card';
	const app = getApp()

	export default {
		data() {
			return {
				baseurl: app.globalData.BaseUrl,
				list: [],
				id: uni.getStorageSync('id')
			}
		},
		methods: {
			editOrder(id, val, index) {
				let that = this
				console.log(id, index)
				uni.request({
					url: this.$data.baseurl + 'editOrder',
					method: "POST",
					data: {
						_id: id,
						status: val
					},
					success(res) {
						if (res.data.code == 1) {
							uni.showToast({
								title: '操作成功',
								icon: "none"
							})
							that.list[index].status = val


						} else {
							uni.showToast({
								title: '操作失败',
								icon: "none"
							})
						}
					}
				})
			}
		},
		components: {
			van_card
		},
		// 挂载
		mounted() {
			let that = this
			uni.request({
				url: this.$data.baseurl + 'getMyGoods',
				method: "POST",
				data: {
					user_id: uni.getStorageSync('id')
				},
				success(res) {
					console.log(res.data.data)
					that.$data.list.push(...res.data.data)
				}
			})
		},
	}
</script>

<style>
	.pingfang {
		font-family: PingFang;
	}

	.button {
		display: inline-block;
		border-radius: 7px;
		border: none;
		background: #1875FF;
		color: white;
		font-family: inherit;
		text-align: center;
		font-size: 13px;
		box-shadow: 0px 14px 56px -11px #1875FF;
		width: 8em;
		padding: 1em;
		transition: all 0.4s;
		display: flex;
		justify-content: center;
		cursor: pointer;
		align-items: center;
	}

	.button span {
		cursor: pointer;
		display: inline-block;
		position: relative;
		transition: 0.4s;
	}

	.button span:after {
		content: '';
		position: absolute;
		opacity: 0;
		top: 0;
		right: -20px;
		transition: 0.7s;
	}

	.button:hover span {
		padding-right: 3.55em;
	}

	.button:hover span:after {
		opacity: 4;
		right: 0;
	}
</style>
