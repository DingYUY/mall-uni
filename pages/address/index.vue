<template>
	<view>
		<view class="add-address flex flex-col justify-center items-center w-full">
			<button class="button text-sm text-white h-12 w-full" style="background: #3787ff; line-height: 3rem; border-radius: 5px 5px 0 0;">新增地址</button>
		</view>
	</view>
</template>

<script>
	export default {
		data() {
			return {
				
			}
		},
		methods: {
			
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
</style>
