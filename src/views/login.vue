<template>
  <div>
    <el-card class="login-form-layout">
      <el-form autocomplete="on" :model="loginForm" ref="loginForm" label-position="left">
        <div style="text-align: center">
          <svg-icon icon-class="login-mall" style="width: 56px;height: 56px"></svg-icon>
        </div>
        <h2 class="login-title color-main">欢迎登录二手平台！</h2>
        <el-form-item prop="username">
          <el-input name="username" type="text" v-model="loginForm.username" autocomplete="on" placeholder="请输入用户名">
            <span slot="prefix">
              <svg-icon icon-class="user" class="color-main"></svg-icon>
            </span>
          </el-input>
        </el-form-item>
        <el-form-item prop="password">
          <el-input name="password" :type="pwdType" @keyup.enter.native="handleLogin" v-model="loginForm.password"
            autocomplete="on" placeholder="请输入密码">
            <span slot="prefix">
              <svg-icon icon-class="password" class="color-main"></svg-icon>
            </span>
            <span slot="suffix" @click="showPwd">
              <svg-icon icon-class="eye" class="color-main"></svg-icon>
            </span>
          </el-input>
        </el-form-item>
        <el-form-item style="margin-bottom: 60px">
          <el-button style="width: 100%"  :loading="loading" @click.native.prevent="handleLogin">登录
          </el-button>
        </el-form-item>
        <el-form-item style="margin-bottom: 10px">
            <el-button style="width: 100%" :loading="loading" @click.native.prevent="handleAdminLogin">
            管理员登录
          </el-button>
          </el-form-item>
        <el-form-item style="margin-bottom: 60px">
          <div style="text-align:right">
            <el-button type="text" @click="toRegisterPage">点击注册</el-button>
          </div>
        </el-form-item>
      </el-form>
    </el-card>
  </div>
</template>

<script>
  export default {
    name: "login",

    //重定向注入
    provide() {
      return {
        reload: this.reload
      }
    },


    data() {
      return {
        loginForm: {
          username: "",
          password: ""
        },
        loading: false,
        pwdType: "password",
      };
    },
    methods: {
      showPwd() {
        if (this.pwdType === "password") {
          this.pwdType = "";
        } else {
          this.pwdType = "password";
        }
      },

      //管理员登录
      handleAdminLogin() {
        var self = this;

        var sendData = {};
        sendData.username = this.loginForm.username
        sendData.password = this.loginForm.password
        // sendData.names=Husername
        var sendJson = JSON.stringify(sendData);

        axios({
          //发送http请求
          method: 'post',
          url: 'http://localhost:9090/admin/login',
          crossDomain: true,
          data: sendJson,
          headers: {
            'Content-Type': 'application/json;charset=UTF-8'
          }
        }).then(response => {
          var length = Object.keys(response.data)
          console.log(response)
          let err_message = response.data.err_message;
          console.log(err_message)

          if (length == 0) {
            alert("用户名密码有误")
          }

          //未出现错误信息
          // if (typeof (err_message) == "undefined")
          else {
            //alert("欢迎管理员")
            window.sessionStorage.setItem('adminobj',JSON.stringify(response.data))
            this.$router.push({
              path: "/admin",
              query: {
                data: response.data
              }
            })
          }

        }).catch(() => {
          alert("errer")
        })


      },

      //跳转登录页面
      toRegisterPage() {
        this.$router.push({
          path: "/register",

        })
      },

      //重新加载方法
      reload() {
        this.isRouterAlive = false
        this.$nextTick(function () {
          this.isRouterAlive = true
        })
      },

      setCookie(c_phone, c_pwd, exdays) {
        var exdate = new Date(); //获取时间
        exdate.setTime(exdate.getTime() + 24 * 60 * 60 * 1000 * exdays); //保存的天数
        //字符串拼接cookie
        window.document.cookie =
          "tel" + "=" + c_phone + ";path=/;expires=" + exdate.toGMTString();
        window.document.cookie =
          "pwd" + "=" + c_pwd + ";path=/;expires=" + exdate.toGMTString();
      },

      //用户登录
      handleLogin() {
        var self = this;

        var sendData = {};
        sendData.username = this.loginForm.username
        sendData.password = this.loginForm.password
        // sendData.names=Husername
        var sendJson = JSON.stringify(sendData);

        axios({
          //发送http请求
          method: 'post',
          url: 'http://localhost:9090/user/login',
          crossDomain: true,
          data: sendJson,
          headers: {
            'Content-Type': 'application/json;charset=UTF-8'
          }
        }).then(response => {
          console.log(response)
          let err_message = response.data.err_message;
          console.log(err_message)

          if (err_message == "用户名密码错误") {
            alert("用户名密码有误")
          }

          //未出现错误信息
          if (typeof (err_message) == "undefined") {
            //alert(response.data.telephonenum)
            this.$cookie.set('username',response.data.username);
            this.$cookie.set('password',response.data.password)
            this.$cookie.set('telephonenum',response.data.telephonenum)
            window.sessionStorage.setItem('userobj',JSON.stringify(response.data))
            this.$router.push({
              path: "/main",
              query: {
                data: response.data
              }
            })
          }

        }).catch(() => {
          alert("errer")
        })
      }
    }
  };
</script>

<style scoped>
  .login-form-layout {
    position: absolute;
    left: 0;
    right: 0;
    width: 360px;
    margin: 140px auto;
    border-top: 10px solid #409eff;
  }

  .login-title {
    text-align: center;
  }

  .login-center-layout {
    background: #409eff;
    width: auto;
    height: auto;
    max-width: 100%;
    max-height: 100%;
    margin-top: 200px;
  }
</style>