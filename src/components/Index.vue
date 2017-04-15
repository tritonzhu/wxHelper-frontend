<template>
  <div class="container">
    <div class="panel panel-default">
      <div class="panel-body">
        <div class="row">
          <div class="col-sm-12 user-info">
            <img v-bind:src="userAvatarSrc" class="avatar">
            <span class="user-name">{{ userName }}</span>
            <a class="pull-right" href="/api/logout">退出登录</a>
          </div>
        </div>
      </div>
    </div>
    <div class="row">
      <div class="col-sm-4">
        <div class="panel panel-default">
          <div class="panel-heading"><span>群列表</span></div>
          <div class="panel-body">
            <group-box v-for="group in groups" v-bind:data-user-name="group.user_name" v-bind:group="group"
                       v-bind:selected="selectedGroup" v-on:select="selectGroup"></group-box>
          </div>
        </div>
      </div>
      <div class="col-sm-8">
        <div class="panel panel-default">
          <div class="panel-heading"><span>群成员({{ groupMemberNumber }})</span></div>
          <div class="panel-body">

          </div>
        </div>
      </div>
    </div>
    </div>
</template>

<script>
import groupBox from './GroupBox.vue'
export default {
  name: 'index',
  methods: {
    loadFriends: function () {
      this.$http.get('/api/friends').then(
        response => {
          let user = response.data.friends[0]
          this.userAvatarSrc = '/api/friends/' + user.user_name + '/avatar'
          this.userName = user.name
        },
        response => {
          console.error('get response error')
          console.log(response)
        }
      )
    },
    loadGroups: function () {
      this.$http.get('/api/groups').then(
        response => {
          this.groups = response.data.groups
        },
        response => {
          console.error('get response error')
          console.log(response)
        }
      )
    },
    selectGroup: function (div) {
      this.selectedGroup = div.dataset.userName
      this.$http.get('/api/groups/' + this.selectedGroup + '/members').then(
        response => {
          let members = response.data.members
          this.groupMemberNumber = members.length
        },
        response => {
          console.error('get response error')
          console.log(response)
        }
      )
    }
  },
  mounted () {
    this.loadFriends()
    this.loadGroups()
  },
  data: function () {
    return {
      userAvatarSrc: '',
      userName: '',
      groups: [],
      selectedGroup: '',
      groupMemberNumber: 0
    }
  },
  components: {
    groupBox
  }

}
</script>

<style>
  span {
    font-size: 18px;
  }

  .user-info {
    display: inline;
  }

  .avatar {
    width: 48px;
    height: 48px;
  }

  .user-name {
    color: darkcyan;
    margin-left: 10px;
  }

</style>

