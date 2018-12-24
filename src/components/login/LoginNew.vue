<template>
  <div class="login">
    <v-header myTitle="登录"></v-header>
    <div class="main">
      <div class="login-wrap" v-show="showLogin">
        <!-- <h3>登录</h3> -->
        <p v-show="showTishi">{{tishi}}</p>
        <input type="text" placeholder="请输入用户名" v-model="username">
        <input type="password" placeholder="请输入密码" v-model="password">
        <button v-on:click="login">登录</button>
        <span v-on:click="ToRegister">没有账号？马上注册</span>
      </div>

      <div class="register-wrap" v-show="showRegister">
        <!-- <h3>注册</h3> -->
        <p v-show="showTishi">{{tishi}}</p>
        <input type="text" placeholder="请输入用户名" v-model="newUsername">
        <input type="password" placeholder="请输入密码" v-model="newPassword">
        <input type="text" placeholder="请输入昵称" v-model="newCname">
        <button v-on:click="register">注册</button>
        <span v-on:click="ToLogin">已有账号？马上登录</span>
      </div>
    </div>
  </div>
</template>

<script>
import SimpleHeader from "../common/SimpleHeader";
import { setCookie, getCookie } from "../../assets/js/cookie.js";
import { base64Encode } from "../../assets/js/base.js";
export default {
  data() {
    return {
      showLogin: true,
      showRegister: false,
      showTishi: false,
      tishi: "",
      username: "",
      password: "",
      newUsername: "",
      newPassword: "",
      newCname: "",
      rootUrl: process.env.API
    };
  },
  mounted() {
    /*页面挂载获取cookie，如果存在username的cookie，则跳转到主页，不需登录*/
    if (getCookie("username")) {
      this.$router.push("/user");
    }
  },
  components: {
    "v-header": SimpleHeader
  },
  methods: {
    login() {
      if (this.username == "" || this.password == "") {
        alert("请输入用户名或密码");
      } else {
        let data = {
          userName: this.username,
          psd: base64Encode(this.password)
        };
        console.log(rootUrl)
        /*接口请求*/
        this.$http.post(rootUrl + "user/checkLogIn", data).then(res => {
          console.log(res);
          var resData = res.data;
          var code = resData.code;
          if (code == 200) {
            this.tishi = "登录成功";
            this.showTishi = true;
            setCookie("username", this.username, 1000 * 60);
            this.$router.push({
              name: "User",
              params: {
                name: "User",
                data: resData
              }
            });
            // setTimeout(
            //   function() {
            //     this.$router.push("/user");
            //   }.bind(this),
            //   1000
            // );
          } else {
            this.tishi = resData.mes;
            alert(resData.mes);
          }
          //  /*接口的传值是(-1,该用户不存在),(0,密码错误)，同时还会检测管理员账号的值*/
          //   if(res.data == -1){
          //       this.tishi = "该用户不存在"
          //       this.showTishi = true
          //   }else if(res.data == 0){
          //       this.tishi = "密码输入错误"
          //       this.showTishi = true
          //   }else if(res.data == 'admin'){
          //   /*路由跳转this.$router.push*/
          //       this.$router.push('/main')
          //   }else{
          //       this.tishi = "登录成功"
          //       this.showTishi = true
          //       setCookie('username',this.username,1000*60)
          //       setTimeout(function(){
          //           this.$router.push('/home')
          //       }.bind(this),1000)
          //   }
        });
      }
    }
  }
};
</script>

<style>
.login {
  position: relative;
  top: 0;
  left: 0;
  width: 100%;
  min-height: 100%;
  font-size: 0.35rem;
}
.main {
  position: absolute;
  top: 1.5rem;
  left: 0;
  bottom: 0;
  width: 100%;
  box-sizing: border-box;
  padding-top: 2rem;
  overflow: auto;
}
.login-wrap {
  text-align: center;
  top: 0.8rem;
}
input {
  display: block;
  width: 250px;
  height: 40px;
  line-height: 40px;
  margin: 0 auto;
  margin-bottom: 10px;
  outline: none;
  border: 1px solid #888;
  padding: 10px;
  box-sizing: border-box;
}
p {
  color: red;
}
button {
  display: block;
  width: 250px;
  height: 40px;
  line-height: 40px;
  margin: 0 auto;
  border: none;
  background-color: #41b883;
  color: #fff;
  font-size: 16px;
  margin-bottom: 5px;
}
span {
  cursor: pointer;
}
span:hover {
  color: #41b883;
}
</style>