<template>
  <div class="login-container" @click.stop="blank">
    <el-form ref="loginForm" :model="loginForm" class="login-form" auto-complete="on"
             label-position="left">
      <div>
        <img :src="bear"/>
      </div>
      <h3 class="title">Zilin's Blog</h3>
      <el-form-item prop="username">
                <span class="svg-container">
                  <svg-icon icon-class="user"/>
                </span>
        <el-input v-model="loginForm.username" @click.native.stop="greeting" name="username" type="text" auto-complete="on" placeholder="username"/>
      </el-form-item>
      <el-form-item prop="password">
                <span class="svg-container">
                  <svg-icon icon-class="password"/>
                </span>
        <el-input
          @click.native.stop="blindfold"
          :type="pwdType"
          v-model="loginForm.password"
          name="password"
          auto-complete="on"
          placeholder="password"
          @keyup.enter.native="handleLogin"/>
        <span class="show-pwd" @click="showPwd">
                    <svg-icon icon-class="eye"/>
                </span>
      </el-form-item>
      <el-form-item align="center">
        <el-button :loading="loading" type="primary" style="width:100%;" @click.native.prevent="handleLogin">
          Sign in
        </el-button>
        <el-divider></el-divider>
        <el-button :loading="loading" style="width:80%;" @click.native.prevent="handleSignUp">
          Sign up
        </el-button>
      </el-form-item>
    </el-form>
  </div>
</template>

<script>
  import normal from '@/assets/login/stand.png'
  import blindfold from '@/assets/login/close_eyes.png'
  import greeting from '@/assets/login/hello.png'
  import {signUp} from "@/api/user";

  export default {
    name: 'Login',
    data() {
      return {
        img: {
          normal: normal,
          blindfold: blindfold,
          greeting: greeting
        },
        bear: normal,

        loginForm: {
          username: '',
          password: ''
        },
        loading: false,
        pwdType: 'password',
        redirect: undefined
      }
    },
    watch: {
      $route: {
        handler: function (route) {
          this.redirect = route.query && route.query.redirect
        },
        immediate: true
      }
    },
    methods: {
      blank() {
        this.bear = this.img.normal;
      },
      blindfold() {
        this.bear = this.img.blindfold;
      },
      greeting() {
        this.bear = this.img.greeting;
      },

      showPwd() {
        if (this.pwdType === 'password') {
          this.pwdType = ''
        } else {
          this.pwdType = 'password'
        }
      },
      handleLogin() {
        this.$refs.loginForm.validate(valid => {
          if (valid) {
            this.loading = true
            //user/login是一个函数
            this.$store.dispatch('user/login', this.loginForm).then(() => {
              this.loading = false
              //rediect是来login页面之前想去的页面
              if (this.redirect === undefined){
                this.$router.push({path: "/"})
              }else {
                this.$router.push({path: this.redirect})
              }
            }).catch(() => {
              this.loading = false
              console.log('登陆失败')
              this.$message.error('登陆失败')
            })
          } else {
            console.log('error submit!!')
            return false
          }
        })
      },
      handleSignUp(){
        this.$refs.loginForm.validate(valid => {
          if (valid) {
            this.loading = true
            //user/login是一个函数
            signUp(this.loginForm).then(() => {
              this.$message.success("注册成功, 请按照注册的用户名和密码登录")
              this.loading = false
            }).catch(() => {
              this.loading = false
            })
          } else {
            console.log('error submit!!')
            return false
          }
        })
      }
    }
  }
</script>

<style rel="stylesheet/scss" lang="scss">
  $bg: #2d3a4b;
  $light_gray: #eee;

  /* reset element-ui css */
  .login-container {
    .el-input {
      display: inline-block;
      height: 47px;
      width: 85%;
      input {
        background: transparent;
        border: 0px;
        -webkit-appearance: none;
        border-radius: 0px;
        padding: 12px 5px 12px 15px;
        height: 47px;
        &:-webkit-autofill {
          -webkit-box-shadow: 0 0 0px 1000px $bg inset !important;
          -webkit-text-fill-color: #fff !important;
        }
      }
    }
    .el-form-item {
      border: 1px solid rgba(255, 255, 255, 0.1);
      border-radius: 5px;
      color: #454545;
      .el-form-item__content {
        border-bottom: 1px solid #dcdfe6;
      }
    }
  }

</style>

<style rel="stylesheet/scss" lang="scss" scoped>
  $bg: #2d3a4b;
  $dark_gray: #889aa4;
  $light_gray: #eee;
  .login-container {
    position: fixed;
    height: 100%;
    width: 100%;
    /*background-color: #b8e5f8;*/
    background-size:cover;
    background-position: 50% 80%;
    background-image: url("~@/assets/login/background.png");
    .login-form {
      position: absolute;
      left: 0;
      right: 0;
      width: 432px;
      max-width: 100%;
      padding: 35px 35px 15px 35px;
      margin: 120px auto;
      background-color: #fff;

      div>img{
        position: absolute;
        top: 0;
        left: 50%;
        width: 5rem;
        -webkit-transform: translate(-50%, -83%);
        transform: translate(-50%, -83%);
        z-index: 1;
      }
    }
    .tips {
      font-size: 14px;
      margin-bottom: 10px;
      span {
        &:first-of-type {
          margin-right: 16px;
        }
      }
    }
    .svg-container {
      padding: 6px 5px 6px 15px;
      vertical-align: middle;
      width: 30px;
      display: inline-block;
    }
    .title {
      font-size: 26px;
      font-weight: 400;
      color: #0084ff;
      margin: 0px auto 40px auto;
      text-align: center;
      font-weight: bold;
    }
    .show-pwd {
      position: absolute;
      right: 10px;
      top: 7px;
      font-size: 16px;
      color: $dark_gray;
      cursor: pointer;
      user-select: none;
    }
  }
</style>
