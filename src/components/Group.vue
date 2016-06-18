<template>
  <div class="c-group">
    <div class="ui grid" v-show="group.master">
      <div class="four wide column">
        <a v-if="group.master" v-link="{path: '/users/' + group.master._id}"><img class="avatar" :src="group.master.avatar"></a>
      </div>

      <div class="eight wide column">
        <div class="ui segment">
          <a v-link="{path: '/groups/' + group._id}"><img class="photo" v-if="group.cover" :src="group.cover"/></a>
          <h3 class="title"><a v-link="{path: '/groups/' + group._id}">{{group.name}}</a></h3>
          <pre class="content">{{group.description}}</pre>
          <div>
            <span class="tag">{{group._id | idToFromNow}}</span>
            <span class="tag right">
              <a v-link="{path: '/groups/' + group._id}">成员({{group.memberCount}})</a>
              <div class="ui inline dropdown">
                <div class="text"></div>
                <i class="dropdown icon"></i>
                <div class="menu">
                  <div class="item" data-text="" @click="recommend('group', group._id)">推荐置顶</div>
                  <div class="item" data-text="" @click="report('group', group._id)">举报</div>
                  <div class="divider" v-if="isMaster"></div>
                  <div class="item" data-text="" v-if="isMaster" v-link="{path: '/groups/' + group._id + '/edit'}">编辑</div>
                  <div class="item" data-text="" v-if="uid && !isMaster && !group.hasJoined" @click="joinGroup">申请加入</div>
                  <div class="item" data-text="" v-if="uid && !isMaster && group.hasJoined" @click="leaveGroup">退出诗社</div>
                </div>
              </div>
            </span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import API from '../services/API'
import User from '../mixins/User'
import Redirect from '../mixins/Redirect'

export default {
  mixins: [User, Redirect],
  props: ['group'],
  ready() {
    $('.ui.dropdown').dropdown()
    $('.ui.dropdown .item').removeClass('active')
  },
  methods: {
    recommend: API.recommend,
    report: API.report,
    joinGroup: function () {
      const groupId = this.group._id
      return API.joinGroup(groupId)
    },
    leaveGroup: function () {
      const groupId = this.group._id
      return API.leaveGroup(groupId)
    }
  },
  computed: {
    isMaster: function () {
      return this.uid === (this.group.master && this.group.master._id)
    }
  }
}
</script>

<style lang="stylus">
@import "../variables.styl"

.c-group
  width $width
  margin 0 auto
  .photo
    width 100%
  .avatar
    border-radius 3px
    width 64px
    height 64px
    box-shadow  0 1px 2px #aaa
    float right
  .tag
    font-family $menuFont
    font-size 13px
    margin-right 5px
    color #999
    &.right
      float right
      margin-right 0
      a
        color #999
</style>
