<template>
  <form class="form card">
    <div class="card_header" style="display: flex; justify-content: center; align-items: center;">
      <svg height="24" width="24" viewBox="0 0 24 24" xmlns="http://www.w3.org/2000/svg">
        <path d="M0 0h24v24H0z" fill="none"></path>
        <path d="M4 15h2v5h12V4H6v5H4V3a1 1 0 0 1 1-1h14a1 1 0 0 1 1 1v18a1 1 0 0 1-1 1H5a1 1 0 0 1-1-1v-6zm6-4V8l5 4-5 4v-3H2v-2h8z" fill="currentColor"></path>
      </svg>
      <h1 class="form_heading">登录</h1>
    </div>
    <view class="" style="display: flex;flex-direction: column;justify-content: center;align-items: center">
      <div class="field">
        <label for="username">Username</label>
        <input id="username" v-model:value="user.username" placeholder="Username" type="text" name="username" class="input">
      </div>
      <div class="field">
        <label for="password">Password</label>
        <input id="password" placeholder="Password" v-model:value="user.user_password" type="password" name="user_password" class="input">
      </div>
    </view>
    <div class="field" style="display: flex;flex-direction: column">
      <button class="button" @click="creat">register</button>
      <button class="button" @click="gologin">login</button>
    </div>
  </form>
</template>

<script >
const app=getApp();

export default {
  data() {
    return {
      user: {
        username: '',
        user_password: ''
      },
      baseurl: app.globalData.BaseUrl
    }
  },
  methods:{
    creat(){
      let that=this;
      console.log(this.user)
      uni.request({
        url: this.baseurl + 'addUser',
        method: 'POST',
        data: {
          name: that.$data.user.username,
          password: that.$data.user.user_password,
					img_head:"https://c-ssl.dtstatic.com/uploads/blog/202105/30/20210530110411_a06e5.thumb.1000_0.jpeg"
        },
        success(res){
          console.log(res.data)
          if(res.data.code==1){
            uni.showToast({
              title: '注册成功',
              icon: 'success',
              duration: 2000
            });
            uni.setStorageSync('user_img', res.data.data.img_head);
            uni.setStorageSync('token', res.data.data.password);
            uni.setStorageSync('id', res.data.data._id)
            uni.setStorageSync('username', res.data.data.name)
            uni.switchTab({
              url: '/pages/index/index'
            })
          }else {
            uni.showToast({
              title: '登录失败',
              icon: 'none',
              duration: 2000
            });
          }
        }
      })
    },
	gologin(){
		uni.navigateTo({
			url:"/pages/login/index"
		})
	}

  }
}
</script>


<style scoped>
.card {
  width: 190px;
  height: 254px;
  background: #F4F6FB;
  border: 1px solid white;
  box-shadow: 10px 10px 64px 0px rgba(180, 180, 207, 0.75);
  -webkit-box-shadow: 10px 10px 64px 0px rgba(186, 186, 202, 0.75);
  -moz-box-shadow: 10px 10px 64px 0px rgba(208, 208, 231, 0.75);
}

.form {
  padding: 25px;
}

.card_header {
  display: flex;
  align-items: center;
}

.card svg {
  color: #7878bd;
  margin-bottom: 20px;
  margin-right: 5px;
}

.form_heading {
  padding-bottom: 20px;
  font-size: 21px;
  color: #7878bd;
}

.field {
  padding-bottom: 10px;
}

.input {
  border-radius: 5px;
  background-color: #e9e9f7;
  padding: 5px;
  width: 90%;
  color: #7a7ab3;
  border: 1px solid #dadaf7
}

.input:focus-visible {
  outline: 1px solid #aeaed6;
}

.input::placeholder {
  color: #bcbcdf;
}

label {
  color: #B2BAC8;
  font-size: 14px;
  display: block;
  padding-bottom: 4px;
}

button {
  background-color: #7878bd;
  margin-top: 10px;
  font-size: 14px;
  padding: 7px 12px;
  height: auto;
  font-weight: 500;
  color: white;
  width: 50%;
}

button:hover {
  background-color: #5f5f9c;
}






</style>
