<template>
    <view style="height: 100%">
        <view class="flex justify-between items-center pingfang" style="padding: 12px">
            <view>收货地址</view>
        </view>

        <view style="padding: 30rpx; width: 85%; background-color: white" class="mx-auto pingfang rounded-xl shadow-xl flex flex-col">
            <view class="flex items-center">
                <image src="/static/images/icon/user.png" style="width: 24px; height: 24px"></image>
                <view style="font-size: 12px; padding-left: 12px">{{ address[0].name }}</view>
            </view>

            <view class="flex items-center" style="margin-top: 20rpx">
                <image src="/static/images/icon/address.png" style="width: 24px; height: 24px"></image>
                <view style="font-size: 12px; padding-left: 12px; width: 80%">{{ address[0].address }}</view>
            </view>

            <view class="flex items-center" style="margin-top: 20rpx">
                <image src="/static/images/icon/call.png" style="width: 24px; height: 24px"></image>
                <view style="font-size: 12px; padding-left: 12px">{{address[0].phone}}</view>
            </view>
        </view>

        <view class="mx-auto" style="width: 90%; padding: 12px; font-size: 12px ;">付款方式</view>

        <view style="padding: 30rpx; width: 85%; background-color: white" class="mx-auto pingfang rounded-xl shadow-xl flex flex-col">
                    <view class="flex" style="font-size: 12px">二手交易支付</view>
        </view>

        <view class="flex justify-between p-2 pingfang font-blod" style="margin-top: 90rpx; padding: 12px">
            <view>共计</view>
            <view>$ {{ sum }}</view>
        </view>

        <view
            @click="pay"
            style="width: 85%; background-color: rgb(24, 144, 224); padding: 30rpx; margin-top: 90rpx"
            class="flex justify-center items-center mx-auto rounded-xl text-white pingfang"
        >
            立即支付
        </view>
    </view>
</template>

<script>
// pages/get_shop_jiesuan/index.js
const app=getApp()
export default {
    data() {
        return {
          baseurl:app.globalData.BaseUrl,
          address:[],
          shop_list:[],
          sum:0,
        };
    }
    /**
     * 生命周期函数--监听页面加载
     */,
    onLoad(options) {

      this.$data.shop_list.push(...JSON.parse(decodeURIComponent(options.arr)))

      this.$data.shop_list.forEach(item=>{
        this.$data.sum+=item.price*item.count
      })


       uni.request({
         url: this.$data.baseurl+'getDefaultAddress',
         method: 'POST',
         data:{
           token:uni.getStorageSync('token')
         },
         success: (res) => {
           console.log(res.data.code)
           if(res.data.code==1){
              this.$data.address.push(...res.data.data)
           }else {
             uni.showToast({
               title: '请在电脑端添加地址',
               icon: 'none',
               duration: 2000
             });

           }
         }
       })

        uni.loadFontFace({
            family: 'PingFang',
            source: 'url("https://636c-cloud1-1g1p0gb13cd58cde-1316153423.tcb.qcloud.la/%E8%8B%B9%E6%96%B9-%E7%AE%80.ttf?sign=0737c1b101f64545d231a285fcc1b796&t=1672448538")',
            success() {
                console.log('字体下载成功');
            }
        });
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
        onChange() {
            console.log('占位：函数 onChange 未声明');
        },
    //  结算
      pay(){
          let that=this
        that.$data.shop_list.forEach(item=>{
            uni.request({
              url:that.$data.baseurl+'addOrder',
              method:"POST",
              data:{
                custorm_name:that.$data.address[0].name,    //客户名字
                amount:item.price*item.count, //订单 总金额
                status:0, //订单状态
                user_id:uni.getStorageSync('id'), //用户id
                shop_name:item.username, //店铺名字
                shop_id:item.user_id, //店铺id
                goods_name:item.name, //商品名字
                goods_title:item.introduce,  //商品介绍
                goods_id:item.good_id, //商品id
                custorm_address:that.$data.address[0].address+that.$data.address[0].phone, //客户地址
                img:item.img, //商品图片
              },
              success(res){
                 if(res.data.code==1){
                    uni.showToast({
                      title: '支付成功',
                      icon: 'none',
                      duration: 2000,
					  success() {
					  	uni.navigateTo({
					  	  url: '/pages/dingdan/index'
					  	});
					  }
                    });
                   
                 }else{
					 uni.showToast({
					   title: '支付失败',
					   icon: 'none',
					   duration: 2000
					 });
				 }
              }
            })
        })
      }
    },
  components:{
  }
};
</script>
<style>
/* pages/get_shop_jiesuan/index.wxss */

.flex{
  display: flex;
}

.border{
  border:1px solid  rgba(0,0,0,.3)
}


.flex-col{
  flex-direction: column;
}

.text-center{
  text-align: center;
}

.p-1{
  padding: 0.25rem;
}

.p-2{
  padding: 0.5rem;
}


.text-xs{
  font-size: 9px;
}

.justify-center{
  justify-content:center;
}

.justify-between{
  justify-content: space-between;
}

.justify-end{
    justify-content: flex-end;
}

.obj-cover{
  object-fit: cover;
}
.obj-contain{
  object-fit: contain;
}

.w-full{
  width: 100%;
}

.items-center{
  align-items: center;
}

.item-end{
  align-items: flex-end;
}

.pingfang{
  font-family:PingFang;
}
.rounded-xl{
    border-radius: 0.5rem;
}

.rounded-full{
  border-radius: 99%;
}

.border-b-2{
  border-bottom-right-radius: 2px;
    border-bottom-left-radius: 2px;
}

.mx-auto{
  margin-left: auto;
  margin-right: auto;
}


.shadow-xl{
  box-shadow: 1px 9px 50px -12px rgba(0, 0, 0, 0.25)
}

.font-blod{
  font-weight: bold;
}

.text-white{
  color: white;
}

.absolute{
  position: absolute;
}


.fixed{
  position: fixed;
}
.bottom-0{
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
