<template>
  <div class="v-group">
    <nav-menu></nav-menu>
    <user-setting-menu></user-setting-menu>
    <group :group="group"></group>
    <group-members :group="group" :members="groupMembers"></group-members>
    <comment :comments="comments" type="group" :refe="group._id"></comment>
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
      group: {
        _id: ''
      },
      groupMembers: [],
      comments: []
    }
  },
  route: {
    data() {
      const groupId = this.$route.params.groupId
      return {
        group: API.getGroup(groupId),
        groupMembers: API.getGroupMembers(groupId),
        comments: API.getCommentsByGroupId(groupId)
      }
    }
  },
  methods: {
  },
  events: {
    'Comment:success': function () {
      API.getCommentsByGroupId(this.group._id).then(comments => {
        this.comments = comments
      })
    }
  },
  components: {
    'UserSettingMenu': require('../components/User-Setting-Menu.vue'),
    'NavMenu': require('../components/Nav-Menu.vue'),
    'Group': require('../components/Group.vue'),
    'GroupMembers': require('../components/Group-Members.vue'),
    'Comment': require('../components/Comment.vue')
  }
}
</script>