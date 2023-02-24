<template>
	<view class="chat">
		<view class="chat-area page">
			<scroll-view class="overflow-hidden overflow-y-scroll area" scroll-y="true" :scroll-into-view="childrenId">
				<view v-for="(item, index) in chatList" :key="index" :id="'id-'+index">
					<view
						style="display: flex; margin: 10px 10px 10px 10px; justify-content: flex-start; align-items: center; flex-wrap: nowrap;"
						v-if="item.current_id !== currentId">
						<image :src="item.shop_id === currentId ? userImg : shopImg"
							style="display: flex; flex-direction: column; justify-content: flex-start; width: 40px; height: 40px; border-radius: 20px; object-fit: cover;">
							<view
								style="display: inline-block; max-width: 60%; word-break: break-all; background-color: greenyellow; padding: 10px; border-radius: 10px; margin-left: 10px;">
								{{ item.content }}
							</view>
					</view>
					<view v-else
						style="display: flex; margin: 10px 10px 10px 10px; justify-content: flex-end; align-items: center; flex-wrap: nowrap;">
						<view
							style="display: inline-block; max-width: 60%; word-break: break-all; background-color: greenyellow; padding: 10px; border-radius: 10px; margin-right: 10px;">
							{{ item.content }}
						</view>
						<image :src="item.user_id === currentId ? userImg : shopImg"
							style="display: flex; flex-direction: column; justify-content: flex-start; width: 40px; height: 40px; border-radius: 20px; object-fit: cover;">
					</view>
				</view>
			</scroll-view>
		</view>
		<view class="footer">
			<input type="text" class="text" confirm-type="send" maxlength="100" :value="sendText" @input="change"
				placeholder="请输入内容" />
			<button @click="sendMessage" class="button">发送</button>
		</view>
	</view>
</template>

<script>
	const app = getApp()
	export default {
		data() {
			return {
				baseurl: app.globalData.BaseUrl,
				orderMessage: {},
				sendText: '',
				chatList: [],
				sendList: {
					content: '',
					shop_id: '',
					user_id: '',
					current_id: ''
				},
				currentId: '',
				receiveList: [],
				recordList: [],
				shopImg: '',
				userImg: '',
				ws: null,
				timeout: null,
				scrollTop: 0,
				childrenId: ''
			};
		},
		onShow() {
			this.$data.orderMessage = JSON.parse(uni.getStorageSync('orderMessage'))
			this.$data.sendList.current_id = uni.getStorageSync('id')
			this.$data.sendList.shop_id = this.$data.orderMessage.shop_id
			this.$data.sendList.user_id = this.$data.orderMessage.user_id
			this.$data.currentId = uni.getStorageSync('id')
			console.log('orderMessage', this.$data.orderMessage)
			// 商家头像
			let that = this
			uni.request({
				url: this.$data.baseurl + 'getHead',
				method: 'POST',
				data: {
					id: that.$data.orderMessage.shop_id
				},
				success(res) {
					that.$data.shopImg = res.data.data.img_head
				}
			})
			// 客户头像
			uni.request({
				url: this.$data.baseurl + 'getHead',
				method: 'POST',
				data: {
					id: that.$data.orderMessage.user_id
				},
				success(res) {
					that.$data.userImg = res.data.data.img_head
				}
			})
			// 获取聊天历史记录
			uni.request({
				url: this.$data.baseurl + 'getChat',
				method: 'POST',
				data: {
					shop_id: that.$data.orderMessage.shop_id,
					user_id: that.$data.orderMessage.user_id
				},
				success(res) {
					that.$data.recordList = res.data.data
					console.log('聊天记录', that.$data.recordList)
					that.$data.chatList = that.$data.recordList
				}
			})

			this.connect()
		},
		mounted() {
			// this.scrollToBottom()
			this.setScroll()
		},
		updated() {
			// this.scrollToBottom()
			this.setScroll()
		},
		beforeDestroy() {
			this.$data.ws.onClose(() => {
				console.log('离开页面，ws连接断开')
			})
		},
		methods: {
			change(e) {
				this.$data.sendText = e.detail.value
				this.$data.sendList.content = this.$data.sendText
			},
			connect() {
				let that = this
				this.$data.ws = uni.connectSocket({
					// url: 'ws://124.222.246.206:3000',
					url: 'ws://127.0.0.1:3000',
					complete: () => {}
				});
				console.log('ws', this.$data.ws)
				this.$data.ws.onOpen(() => {
					console.log('WebSocket connected');
				});
				this.$data.ws.onClose(() => {
					console.log('WebSocket disconnected');
				});
				this.$data.ws.onError(error => {
					console.error('WebSocket error:', error);
				});
				this.$data.ws.onMessage(res => {
					console.log('接收:', JSON.parse(res.data))
					this.$data.receiveList = JSON.parse(res.data)
					console.log('receiveList', this.$data.receiveList)
					this.$data.chatList = this.$data.recordList.concat(this.$data.receiveList)
					console.log('recordList', this.$data.recordList)
					console.log('chatList', this.$data.chatList)
				});
			},
			sendMessage() {
				let that = this
				if (this.$data.sendText) {
					console.log('发送：', JSON.stringify(this.$data.sendList))
					this.$data.ws.send({
						data: JSON.stringify(this.$data.sendList)
					})
					uni.request({
						url: this.$data.baseurl + 'addChat',
						method: 'POST',
						data: {
							content: this.$data.sendText,
							shop_id: this.$data.orderMessage.shop_id,
							user_id: this.$data.orderMessage.user_id,
							current_id: uni.getStorageSync('id'),
							token: uni.getStorageSync('token')
						},
						success(res) {
							console.log('添加到数据库', res.data)
						}
					})
					this.$data.sendText = ''
				} else {
					uni.showToast({
						title: '请输入内容',
						icon: 'none',
						duration: 2000
					})
				}
			},
			// 滚动到底部
			// scrollToBottom() {
			// 	let that = this
			// 	let query = uni.createSelectorQuery()
			// 	query.select('#scrollview').boundingClientRect()
			// 	query.select('#msglistview').boundingClientRect()
			// 	query.exec((res) => {
			// 		console.log(res)
			// 		if (res[1].height > res[0].height) {
			// 			that.scrollTop = res[1].height - res[0].height
			// 		}
			// 	})
			// }
			setScroll() {
				let len = this.chatList.length
				this.childrenId = 'id-'+(len - 1)
			}
		}
	}
</script>

<style lang="scss">
	.chat {
		height: auto !important;
	}

	.chat-area {
		height: calc(100vh - 40px);
		margin-bottom: 40px;

		.area {
			height: 99%;
			border-radius: 5px;
			background-color: #fdfdfd;
		}
	}

	.footer {
		position: fixed;
		bottom: 0;
		display: flex;
		justify-content: space-between;
		align-items: center;
		flex-wrap: nowrap;
		width: 100%;
		height: 40px;
		background-color: #f3f3f3;
		// box-shadow: 0px -2px 5px #ccc;

		.text {
			width: 80%;
			height: 40px;
			border-radius: 5px;
			border: 1px solid #ccc;
			font-size: 14px;
		}

		.button {
			float: right;
			border-radius: 5px;
			background-color: #00bcd4;
			color: #fff;
			font-size: 14px;
			font-weight: bold;
		}
	}
</style>
