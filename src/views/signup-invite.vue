<template>
<div class="v-signup-invite">
  <div class="mask">
    <div class="container">
      <p class="title">不是诗人</p>
      <p class="subtitle">一个分享与发现的地方</p>
      <div class="ui input big fluid">
        <input class="code" type="text" placeholder="邀请码" v-model="code">
      </div>
      <br>
      <a class="ui button fluid" @click="signupInvite">验证</a>
      <div style="margin-top:5px">
        <a v-link="{path: '/signin'}" style="float:left;color:#fff">登录</a>
        <a v-link="{path: '/forgetPassword'}" style="float:right;color:#fff">忘记密码?</a>
      </div>
    </div>
  </div>
</div>
</template>

<style lang="stylus">
@import "../variables.styl"

.v-signup-invite
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
    .code
      border-radius 4px !important
      border 0 !important
    ::-webkit-input-placeholder
      font-size 15px
</style>

<script>
import API from '../services/API'

export default {
  data() {
    return {
      code: ''
    }
  },
  methods: {
    signupInvite: function () {
      const code = this.code
      if (!/^[0-9A-Za-z]{6}$/.test(code)) {
        toastr.error('请输入有效的邀请码')
        return
      }
      return API.checkInviteCode(code).then(() => {
        this.$route.router.go('/signup?code=' + code)
      })
    }
  }
}
</script>