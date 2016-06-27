<template>
<div class="v-forget-password">
  <div class="mask">
    <div class="container">
      <p class="title">不是诗人</p>
      <p class="subtitle">一个分享与发现的地方</p>
      <div class="ui action left icon input big fluid" style="border-bottom:1px solid #ddd">
        <input class="mobile" type="text" placeholder="手机号" v-model="mobile">
        <i class="user icon"></i>
        <a class="ui button" @click="getCode" :class="{disabled: disabled}">获取验证码{{interval > 0 ? '(' + interval + 's)' : ''}}</a>
      </div>
      <div class="ui left icon input big fluid">
        <input class="password" type="password" placeholder="密码" v-model="password">
        <i class="lock icon"></i>
      </div>
      <div class="ui left icon input big fluid">
        <input class="password" type="password" placeholder="重复密码" v-model="repassword">
        <i class="lock icon"></i>
      </div>
      <div class="ui left icon input big fluid">
        <input class="code" type="text" placeholder="验证码" v-model="code">
        <i class="protect icon"></i>
      </div>
      <br>
      <a class="ui button fluid" @click="resetPassword">重置密码</a>
    </div>
  </div>
</div>
</template>

<style lang="stylus">
@import "../variables.styl"

.v-forget-password
  width 100%
  height 100%
  overflow-y scroll
  background-image url("../../static/img/bg.jpg")
  background-size 100% 100%
  background-repeat no-repeat
  background-attachment fixed
  .button:hover
    color #fff !important
  .mask
    width 100%
    height 100%
    background-color rgba(0, 0, 0, 0.3)
  .container
    width 300px
    text-align center
    margin 0 auto
    padding-top 200px
    .title
      font-size 70px
      color #ffffff
      font-family $themeFont
      margin-bottom 0
    .subtitle
      font-size 17px
      color #ffffff
      font-family $themeFont
      margin-top 0
    .mobile
      border-radius 4px 4px 0 0 !important
      border 0 !important
    .password
      border-radius 0 !important
      border 0 !important
      border-bottom 1px solid #ddd !important;
    .code
      border-radius 0 0 4px 4px !important
      border 0 !important
    ::-webkit-input-placeholder
      font-size 15px
</style>

<script>
import API from '../services/API'

export default {
  data() {
    return {
      mobile: '',
      password: '',
      repassword: '',
      code: '',
      disabled: false,
      interval: 0
    }
  },
  methods: {
    getCode: function () {
      let mobile = this.mobile
      if (!mobile) {
        return toastr.error('请输入手机号')
      }
      mobile = '+86' + mobile
      this.disabled = true
      this.interval = 60
      let handler = setInterval(() => {
        this.interval--
        if (this.interval <= 0) {
          this.disabled = false
          clearInterval(handler)
        }
      }, 1000)
      return API.getCode(mobile, 'resetPassword')
    },
    resetPassword: function () {
      let mobile = this.mobile
      const password = this.password
      const repassword = this.repassword
      const code = this.code
      if (password !== repassword) {
        return toastr.error('两次密码输入不一致')
      }
      mobile = '+86' + mobile
      return API.resetPassword(mobile, password, repassword, code).then(user => {
        for (let key in user) {
          localStorage[key] = user[key]
        }
        API.ajaxSetup()//re setup
        const query = this.$route.query
        if (query && query.redirect) {
          this.$route.router.go(decodeURIComponent(query.redirect))
        } else {
          this.$route.router.go('/users/' + user.uid)
        }
      })
    }
  }
}
</script>