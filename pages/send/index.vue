<template>
	<view>
		<!--    使用vant-uploader-->
		<view style="width: 100%;display: flex;justify-content: center;flex-direction: column;align-items: center;">
			<van_uploader file-list="fileList" style="margin: 0 auto;" max-count="5" deletable="true"
				@after-read="afterRead"></van_uploader>
			<view style="width: 60%;">
				<label class="label" style="margin-top: 10px;padding-left: 15rpx">图片展示</label>
			</view>
			<view class="flex flex-wrap justify-start w-full"
				style="width: 60%;margin: 10px;display: flex;flex-wrap: wrap;">
				<view v-for="(item,index) in fileList	" :key="index" :style="{backgroundImage:'url('+item.url+')'	}"
					class="p-1 bg-cover rounded" style="width: 33%;height: 100px;background-size: cover;margin: 5px;">
					<view @click="delImg(index)"
						style="background-color: rgba(0,0,0,.5);width: 1rem;height: 1rem;border-radius: 99%;color: white;float: right;display: flex;justify-content: center;align-items: center;"
						src="">x</view>
				</view>
			</view>

			<div class="input-group">
				<label class="label">商品名字</label>
				<input autocomplete="off" v-model:value="user.shop_name" name="Email" id="Email" class="input"
					type="email">
				<div></div>
			</div>

			<div class="input-group" style="margin-top: 20px">
				<label class="label">简介</label>
				<textarea name="" style="height: 70px;padding: 10px;" v-model:value="user.shop_introduce" auto-height
					id="" cols="1100" rows="40" class="input"></textarea>
				<div></div>
			</div>

			<view
				style="margin-top: 10px;display: flex;width: 60%;justify-content: space-between;align-items: flex-start;flex-direction: column;">
				<label class="label">价格</label>
				<view style="display: flex;">
					<input type="number" placeholder="请输入价格" @input="TypeInput($event)" class="input"
						v-model:value="sum">
				</view>
			</view>

			<div class="input-group" style="margin-top: 20px">
				<label class="label">发货地址</label>
				<textarea name="" v-model:value="user.address" style="height: 70px;padding: 10px;" auto-height id=""
					cols="1100" rows="40" class="input"></textarea>
				<div></div>
			</div>

			<view
				style="width: 60%;display: flex;justify-content: center;margin: 0 auto;margin-top: 50px;margin-bottom: 100px;">
				<view @click="addShop" style="padding:10px;background: #47abea;color:white;border-radius: 10px"
					class="pingfang">提交商品</view>
			</view>
		</view>

		<customTabBar style="position: fixed;bottom: 0px;width: 100%;" select="1"></customTabBar>
	</view>
</template>

<script>
	const app = getApp();
	import customTabBar from "../../custom-tab-bar/index.vue";
	import van_uploader from '../../static/dist/uploader/index';
	export default {
		components:{
				customTabBar,
				van_uploader
		},
		data() {
			return {
				fileList: [],
				baseurl: app.globalData.BaseUrl,
				sum: 1,
				user: {
					shop_name: '',
					shop_introduce: '',
					address: ""
				}
			}
		},
		mounted() {
			uni.request({
				url: this.$data.baseurl + 'getMyGoods',
				method: "POST",
				data: {
					user_id: uni.getStorageSync('id')
				},
				success(res) {
					console.log(res)
				}
			})
		},
		methods: {



			delImg(index) {
				let that = this
				uni.showToast({
					title: "删除成功",
					success() {
						that.fileList.splice(that.fileList[index], 1)
					}
				})
			},

			TypeInput(e, val) {
				// 只能输入数字的验证;
				const inputType = /[^\d]/g //想限制什么类型在这里换换正则就可以了
				if (inputType.test(e.detail.value)) {
					uni.showToast({
						title: '请输入数字',
						icon: 'none',
						duration: 2000
					})
					this.sum = ''
					return
				} else {
					this.sum = e.detail.value
				}



			},

			afterRead(event) {
				console.log(event)
				let that = this
				const {
					file
				} = event;
				//  上传图片
				uni.uploadFile({ //上传图片
					url: this.$data.baseurl + 'api/upload', //仅为示例，非真实的接口地址
					filePath: file.url,
					name: 'file',
					formData: { //
						user: 'test'
					},
					success(res) {
						let result = JSON.parse(res.data)
						if (result.code == 0) {
							uni.showToast({
								title: '上传成功',
								icon: 'success',
								duration: 2000
							});

							that.$data.fileList.push({
								url: that.$data.baseurl + result.url,
								deletable: true,

							})

						}
					}
				})
			},

			//  添加
			add() {
				this.$data.sum++
			},

			//  减少
			reduce() {
				if (this.$data.sum > 1) {
					this.$data.sum--
				}
			},

			//  添加商品
			addShop() {
				//不能相册不能为空
				if (this.$data.fileList.length == 0) {
					uni.showToast({
						title: '请上传图片',
						icon: 'none',
						duration: 2000
					});
					return
				}

				//商品名字不能为空
				if (this.$data.user.shop_name == '') {
					uni.showToast({
						title: '商品名字不能为空',
						icon: 'none',
						duration: 2000
					});
					return
				}

				//商品简介不能为空
				if (this.$data.user.shop_introduce == '') {
					uni.showToast({
						title: '商品简介不能为空',
						icon: 'none',
						duration: 2000
					});
					return
				}

				//商品地址不能为空
				if (this.$data.user.address == '') {
					uni.showToast({
						title: '商品地址不能为空',
						icon: 'none',
						duration: 2000
					});
					return
				}


				//将图片数据类型转换成 ["/upload/2020-05-20/5ec4f8b0e1b5f.jpg","/upload/2020-05-20/5ec4f8b0e1b5f.jpg"]
				let img = []
				this.$data.fileList.forEach(item => {
					img.push(item.url)
				})
				//  提交商品
				uni.request({
					url: this.$data.baseurl + 'addGoods', 
					method: "POST",
					data: {
						name: this.$data.user.shop_name,
						introduce: this.$data.user.shop_introduce,
						address: this.$data.user.address,
						price: this.$data.sum,
						img: img,
						user_id: uni.getStorageSync('id'),
						username: uni.getStorageSync('username')
					},
					success(res) {
						console.log(res.data)
						if (res.data.code == 1) {
							uni.showToast({
								title: '添加成功',
								icon: 'success',
								duration: 2000,
								success() {
									setTimeout(() => {
										uni.switchTab({
										 url: '/pages/index/index'
										})
									}, 1500)
									this.$data.user.shop_name = ''
									this.$data.user.shop_introduce = ''
									this.$data.user.address = ''
									this.$data.sum = 1
									img = []
								}
							});
						}
					}
				})

			}

		},
	
		onLoad() {
			// uni.loadFontFace({
			// 	family: 'Pacifico',
			// 	source: 'url("https://sungd.github.io/Pacifico.ttf")',
			// 	success() {
			// 		console.log('字体下载成功');
			// 	}
			// });
		},
		onShow() {
			let token = uni.getStorageSync('token') || false
			if (token) {

			} else { //没有token
				console.log('没有token')
				uni.navigateTo({
					url: "/pages/login/index"
				})
			}
		}
	}
</script>

<style >
	.input {
		max-width: 190px;
		height: 44px;
		background-color: #05060f0a;
		border-radius: .5rem;
		padding: 0 1rem;
		border: 2px solid transparent;
		font-size: 1rem;
		transition: border-color .3s cubic-bezier(.25, .01, .25, 1) 0s, color .3s cubic-bezier(.25, .01, .25, 1) 0s, background .2s cubic-bezier(.25, .01, .25, 1) 0s;
	}

	.label {
		display: block;
		margin-bottom: .3rem;
		font-size: .9rem;
		font-weight: bold;
		color: #05060f99;
		transition: color .3s cubic-bezier(.25, .01, .25, 1) 0s;
	}

	.input:hover,
	.input:focus,
	.input-group:hover .input {
		outline: none;
		border-color: #05060f;
	}

	.input-group:hover .label,
	.input:focus {
		color: #05060fc2;
	}

	.pingfang {
		font-family: PingFang;
	}
	
	.van-uploader__upload{
		display: flex;
		justify-content: center;
	}
</style>
