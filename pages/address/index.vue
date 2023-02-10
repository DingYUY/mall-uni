<template>
	<view>
		<view class="add-address flex flex-col justify-center items-center w-full" @click="open">
			<button class="button text-sm text-white h-12 w-full"
				style="background: #3787ff; line-height: 3rem; border-radius: 5px 5px 0 0;">新增地址</button>
		</view>
		<view class="address-list" style="height: calc(100vh - 3rem); overflow-y: auto;">
			<view class="pingfang border-solid border-1 rounded-lg p-2 m-2"
				style="background-color: rgba(243, 244, 246); border-color: rgba(243, 244, 246, 0.3);"
				v-for="(item, index) in addressList" :key="index">
				<view class="flex justify-between">
					<view class="text-sm">
						<view>姓名：{{ item.name }}</view>
						<view>手机号：{{ item.phone }}</view>
						<view>地址：{{ item.address }}</view>
					</view>
					<view class="">
						<button class="text-sm" @click="deleteAddress(item)">删除</button>
					</view>
				</view>
				<view class="">
					<!-- <uni-data-checkbox v-model="value" :localdata="range" @change="setDefaultAddress"></uni-data-checkbox> -->
					<label class="radio">
						<radio style="transform:scale(0.7)" value="" :checked="item.is_default" @click="setDefaultAddress(item)" /><text>默认</text>
					</label>
				</view>
			</view>
		</view>

		<uni-popup ref="popup" type="dialog">
			<uni-popup-dialog mode="input" message="新增成功" :duration="2000" :before-close="true" @close="close"
				@confirm="confirm">
				<template>
					<view class="">
						<uni-forms ref="form" :model-value="formData" :rules="rules" label-position="left"
							validate-trigger="bind" class="form">
							<uni-forms-item label="姓名" name="name" required>
								<uni-easyinput type="text" v-model="formData.name" placeholder="请输入姓名" />
							</uni-forms-item>
							<uni-forms-item label="手机号" name="phone" required>
								<uni-easyinput type="number" v-model="formData.phone" placeholder="请输入手机号码" />
							</uni-forms-item>
							<uni-forms-item label="地址" name="address" required>
								<uni-easyinput type="text" v-model="formData.address" placeholder="请输入地址" />
							</uni-forms-item>
						</uni-forms>
					</view>
				</template>
			</uni-popup-dialog>
		</uni-popup>
	</view>
</template>

<script>
	const app = getApp()

	export default {
		data() {
			return {
				baseurl: app.globalData.BaseUrl,
				addressList: [],
				formData: {
					name: '',
					phone: '',
					address: ''
				},
				rules: {
					name: {
						rules: [{
								required: true,
								errorMessage: '请输入姓名'
							},
							{
								minLength: 1,
								maxLength: 10,
								errorMessage: '姓名长度在1到10个字符'
							}
						]
					},
					phone: {
						rules: [{
								required: true,
								errorMessage: '请输入手机号'
							},
							{
								format: 'number',
								validateFunction: function(rule, value, data, callback) {
									const reg = /^1[345789]\d{9}$/
									if (!reg.test(value)) {
										callback(new Error('请输入11位手机号'))
									}
									return true
								},
								errorMessage: '请输入正确的手机号'
							}
						]
					},
					address: {
						rules: [{
								required: true,
								errorMessage: '请输入地址'
							},
							{
								minLength: 1,
								maxLength: 50,
								errorMessage: '地址长度在1到50个字符'
							}
						]
					}
				}
			}
		},
		onReady() {
			// 设置表单校验规则，必须在节点渲染完毕后执行
			this.$refs.form.setRules(this.rules)
		},
		onShow() {
			this.getAddress()
		},
		methods: {
			open() {
				this.$refs.popup.open()
			},
			close() {
				// TODO 做一些其他的事情，before-close 为true的情况下，手动执行 close 才会关闭对话框
				// ...
				this.$refs.popup.close()
			},
			confirm(value) {
				// 输入框的值
				console.log(value)
				// TODO 做一些其他的事情，手动执行 close 才会关闭对话框
				// ...
				this.$refs.form.validate().then(res => {
					console.log('formConfirm', res)
					uni.request({
						url: this.$data.baseurl + 'addAddress',
						method: 'POST',
						data: {
							token: uni.getStorageSync('token'),
							name: this.$data.formData.name,
							phone: this.$data.formData.phone,
							address: this.$data.formData.address,
							is_default: false
						},
						success: (res) => {
							console.log('add', res)
							this.getAddress()
						},
						fail: (err) => {
							console.log(err)
						}
					})
					this.$refs.popup.close()
				}).catch(err => {
					console.log(err)
				})
			},
			// 获取地址
			getAddress() {
				let that = this
				uni.request({
					url: this.$data.baseurl + 'getAddress',
					method: 'POST',
					data: {
						token: uni.getStorageSync('token')
					},
					success(res) {
						console.log('getAddress', res)
						that.$data.addressList = []
						that.$data.addressList.push(...res.data.data)
						that.$data.addressList.forEach(item => {
							item.is_del = false
						})
					},
					fail(err) {
						console.log(err)
					}
				})
			},
			// 设置默认地址
			setDefaultAddress(items) {
				this.$data.addressList.forEach(item => {
					if (items == item) { //如果是当前点击的地址
						item.is_default = true
						uni.request({
							url: this.$data.baseurl + 'setDefaultAddress',
							method: 'POST',
							data: {
								_id: item._id
							},
							success() {
								console.log('设置成功')
							}
						})
					} else { //如果不是当前点击的地址
						item.is_default = false
					}
				})
			},
			deleteAddress(item) {
				let that = this
				uni.request({
					url: this.$data.baseurl + 'delAddress',
					method: 'POST',
					data: {
						id: item._id
					},
					success() {
						console.log('删除成功')
						that.getAddress()
					}
				})
			}
		}
	}
</script>

<style lang="scss">
	.add-address {
		position: fixed;
		bottom: 0;
		left: 0;

		.button {
			border-radius: 0;
		}
	}

	.form {
		padding: 15px;
		background-color: #fff;
	}

	.form-item {
		display: flex;
		align-items: center;
	}
</style>
