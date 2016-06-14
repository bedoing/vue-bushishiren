<template>
  <div class="c-group-members">
    <div class="ui grid">
      
      <div class="four wide column"></div>

      <div class="eight wide column">
        <div class="ui segment">
          <div class="ui minimal comments">
            <h3 class="ui dividing header">诗社成员</h3>
            <div class="comment" v-for="member in members">
              <a class="avatar" v-link="{path: '/users/' + member.user._id}">
                <img :src="member.user.avatar">
              </a>
              <div class="content">
                <a class="author" v-link="{path: '/users/' + member.user._id}">{{member.user.name}}</a>
                <div class="metadata">
                  <span class="date">{{member._id | idToFromNow}}加入</span>
                </div>
                <div class="text">{{member.user.bio || member.user.name}}</div>
                <div class="actions">
                  <a class="reply" v-if="isMaster(member.user) && (member.user._id !== group.master._id)" @click.prevent="removeUserFromGroup(member.user._id)">移出诗社</a>
                </div>
              </div>
            </div>
            <div class="loadMore" @click.prevent="loadMore" v-show="hasMore"><i class="angle down icon"></i></div>
          </div>
        </div>
      </div>
    </div>

  </div>
</template>

<script>
import API from '../services/API'
import User from '../mixins/User'

export default {
  mixins: [User],
  data() {
    return {
      hasMore: true
    }
  },
  props: ['group', 'members'],
  methods: {
    isMaster: function () {
      return this.uid === (this.group && this.group.master && this.group.master._id)
    },
    removeUserFromGroup: function (uid) {
      const groupId = this.$route.params.groupId
      return API.removeUserFromGroup(groupId, uid).then(() => {
        this.members = this.members.filter(function (member) {
          return member.user._id !== uid
        })
      })
    },
    loadMore: function () {
      const groupId = this.$route.params.groupId
      const limit = 20
      const lastId = this.members.length ? this.members[this.members.length - 1]._id : ''
      API.getGroupMembers(groupId, limit, lastId).then(_members => {
        this.members = this.members.concat(_members)
        if (!_members.length) {
          this.hasMore = false
        }
      })
    }
  }
}
</script>

<style lang="stylus">
@import "../variables.styl"

.c-group-members
  width $width
  margin 0 auto
  .loadMore
    cursor pointer
    text-align center
    margin-bottom -14px
    font-size 18px
    line-height 24px
    color #aaa
</style>
