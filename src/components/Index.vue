<template>
  <div class="container">
    <div class="heading">
      <div class="user-info">
        <img :src="userAvatarSrc" class="avatar">
        <span class="user-name">{{ userName }}</span>
      </div>
      <!--<el-button type="danger" icon="el-icon-circle-close" class="logout-btn">关闭</el-button>-->
      <el-button class="logout-btn" @click="logout" size="small">注销</el-button>
    </div>
    <el-row :gutter="10">
      <el-col :span="8">
        <el-card v-loading.body="loadingGroups">
          <div slot="header">群列表({{ groups.length }})</div>
          <group-box v-for="group in groups" :key="group.user_name" :data-user-name="group.user_name" :group="group"
                     :selectedGroup="selectedGroup" @select="selectGroup"></group-box>
        </el-card>
      </el-col>
      <el-col :span="16">
        <el-card v-loading.body="loadingMembers">
          <el-tabs value="nonFriends">
            <!--<el-tab-pane disabled=True>-->
              <!--<span slot="label">{{ groupName }} ({{ groupMemberNumber }})</span>-->
            <!--</el-tab-pane>-->
            <el-tab-pane name="nonFriends">
              <span slot="label">陌生人({{ nonFriends.length }})</span>
              non friends
            </el-tab-pane>
            <el-tab-pane name="friends">
              <span slot="label">好友({{ friends.length }})</span>
              friends
            </el-tab-pane>
          </el-tabs>
        </el-card>
      </el-col>
    </el-row>
    </div>
</template>

<script>
import groupBox from './GroupBox.vue'
import ElButton from '../../node_modules/element-ui/packages/button/src/button'
import ElTabPane from '../../node_modules/element-ui/packages/tabs/src/tab-pane'

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
      this.loadingGroups = true
      this.$http.get('/api/groups').then(
        response => {
          this.groups = response.data.groups
          this.loadingGroups = false
        },
        response => {
          console.error('get response error')
          console.log(response)
        }
      )
    },
    selectGroup: function (div) {
      this.selectedGroup = div.dataset.userName
      this.groupName = div.textContent
      this.loadingMembers = true
      this.$http.get('/api/groups/' + this.selectedGroup + '/members').then(
        response => {
          let members = response.data.members
          this.nonFriends = members.filter(
              member => {
                return member.is_friend === false
              }
              )
          this.friends = members.filter(
              member => {
                return member.is_friend
              }
              )
          this.loadingMembers = false
        },
        response => {
          console.error('get response error')
          console.log(response)
        }
      )
    },
    logout: function () {
      this.$http.get('/api/logout').then(
        response => {
          this.$router.push({path: 'login'})
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
      groupName: '群成员',
      nonFriends: [],
      friends: [],
      loadingGroups: false,
      loadingMembers: false
    }
  },
  components: {
    ElTabPane, ElButton, groupBox
  }

}
</script>

<style>
  .container {
    width: 70%;
    min-width: 800px;
    margin: 0 auto;
  }

  .heading {
    display: flex;
    background-color: #8492A6;
    /*border-radius: 4px;*/
    padding: 10px;
    align-items: center;
    align-content: space-between;
    justify-content: space-between;
    margin-bottom: 20px;
  }

  .user-info {
    display: flex;
    align-items: center;
    font-size: 18px;
  }

  .avatar {
    width: 48px;
    height: 48px;
  }


  .user-name {
    color: #1F2D3D;
    margin-left: 10px;
    vertical-align: middle;
  }

  .logout-btn {
    font-size: 14px;
  }

</style>

