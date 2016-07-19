<template>
  <div class="c-user-menu">
    <div class="ui vertical right tabular menu">
      <a class="item" :class="{'active': path === '' || path === '/poems'}" v-link="{path: urlPrefix}">诗</a>
      <a class="item" :class="{'active': path === '/poetries'}" v-link="{path: urlPrefix + '/poetries'}">诗集</a>
      <a class="item" :class="{'active': path === '/groups'}" v-link="{path: urlPrefix + '/groups'}">诗社</a>
      <a class="item" :class="{'active': path === '/archive'}" v-link="{path: urlPrefix + '/archive'}">归档</a>
      <a class="item" :class="{'active': path === '/messages'}" v-link="{path: urlPrefix + '/messages'}" v-if="uid === userId"><div class="ui button label" v-if="messagesUnread">{{messagesUnread}}</div>私信</a>
      <a class="item" :class="{'active': path === '/notifications'}" v-link="{path: urlPrefix + '/notifications'}" v-if="uid === userId"><div class="ui button label" v-if="notificationsUnread">{{notificationsUnread}}</div>通知</a>
      <a class="item" v-if="uid === userId" @click="getInviteCode">邀请码</a>
    </div>
  </div>
</template>

<style lang="stylus">

</style>

<script>
import API from '../services/API'
import User from '../mixins/User'

export default {
  mixins: [User],
  data() {
    return {
      messagesUnread: 0,
      notificationsUnread: 0,
      inviteCode: ''
    }
  },
  ready: function () {
    if (this.uid) {
      API.getUnreadMessagesCount().then(_messagesUnread => {
        this.messagesUnread = _messagesUnread
      })
      API.getUnreadNotificationsCount().then(_notificationsUnread => {
        this.notificationsUnread = _notificationsUnread
      })
    }
  },
  methods: {
    getInviteCode: function () {
      return API.getInviteCode().then(inviteCode => {
        this.inviteCode = inviteCode
        alert('您的邀请码为: ' + inviteCode)
      })
    }
  },
  computed: {
    userId: function () {
      return this.$route.params.uid;
    },
    path: function () {
      return this.$route.path.replace('/users/' + this.userId, '')
    },
    urlPrefix: function () {
      return '/users/' + this.userId
    }
  }
}
</script>