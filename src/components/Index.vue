<template>
  <div class="container">
    <div class="heading">
      <div class="user-info">
        <img :src="userAvatarSrc" class="avatar">
        <span class="user-name">{{ user.name }}</span>
      </div>
      <!--<el-button type="danger" icon="el-icon-circle-close" class="logout-btn">关闭</el-button>-->
      <el-button class="logout-btn" @click="logout" size="small">注销</el-button>
    </div>
    <el-row :gutter="10">
      <el-col :span="8">
        <el-card v-loading.body="loadingGroups">
          <div slot="header">
            <span>群列表({{ groups.length }})</span>
          </div>
          <group-box v-for="group in groups" :key="group.user_name" :group="group"
                     :selectedGroup="selectedGroup" @select="selectGroup"></group-box>
        </el-card>
      </el-col>
      <el-col :span="16">
        <el-card v-loading.body="loadingMembers">
          <el-tabs value="nonFriends" @tab-click="selectTab">
            <!--<el-tab-pane disabled=True>-->
              <!--<span slot="label">{{ groupName }} ({{ groupMemberNumber }})</span>-->
            <!--</el-tab-pane>-->
            <el-tab-pane name="nonFriends">
              <span slot="label">陌生人({{ nonFriends.length }})</span>
              <div>
              <user-box v-for="(user, index) in nonFriends" :key="user.user_name" :user="user" :isFriend="false" ref="nonFriendsBox" :group="selectedGroup" @select="selectUser"></user-box>
              </div>
            </el-tab-pane>
            <el-tab-pane name="inAdding">
              <span slot="label">添加中({{ friendsInAdding.length }})</span>
              <div>
                <user-box v-for="(user, index) in friendsInAdding" :key="user.user_name" :user="user" :isFriend="true" ref="inAddingBox" :group="selectedGroup" @select="selectUser"></user-box>
              </div>
            </el-tab-pane>
            <el-tab-pane name="friends">
              <span slot="label">好友({{ friends.length }})</span>
              <user-box v-for="user in friends" :key="user.user_name" :user="user" :isFriend="true" :group="selectedGroup" @select="selectUser"></user-box>
            </el-tab-pane>
          </el-tabs>
          <div class="search-box">
            <el-input v-model="searchText" placeholder="搜索联系人" icon="search" @change="searchFriends"></el-input>
          </div>
          <div class="select-box">
            <el-checkbox v-model="selectAll" :disabled="disableAdd" @change="selectAllUsers">全选</el-checkbox>
            <el-button size="small" type="primary" class="add-btn" :disabled="disableAdd" @click="confirmAdd"> 加为好友</el-button>
          </div>
        </el-card>
      </el-col>
    </el-row>
    <el-dialog v-model="showDialog" title="添加联系人">
      <el-form labelWidth="80px">
        <el-form-item label="验证消息">
          <el-input v-model="verifyMessage" autofocus="autofocus" placeholder="请输入验证消息">
          </el-input>
        </el-form-item>
      </el-form>
      <div slot="footer" class="dialog-footer">
        <el-button @click="showDialog = false">取消</el-button>
        <el-button type="primary" @click="addFriends">确定</el-button>
      </div>
    </el-dialog>
    </div>
</template>

<script>
import groupBox from './GroupBox.vue'
import ElButton from 'element-ui/packages/button/src/button'
import ElTabPane from 'element-ui/packages/tabs/src/tab-pane'
import userBox from './UserBox.vue'
import ElCheckbox from 'element-ui/packages/checkbox/src/checkbox'
import ElDialog from 'element-ui/packages/dialog/src/component'
import ElFormItem from 'element-ui/packages/form/src/form-item'
import ElForm from 'element-ui/packages/form/src/form'
import ElInput from 'element-ui/packages/input/src/input'

export default {
  name: 'index',
  methods: {
    checkLogin: function () {
      this.$http.get('/api/checklogin').then(
        response => {
          if (response.data !== 'true') {
            this.logout()
          }
        },
        response => {
          this.logout()
        }
      )
      setTimeout(() => this.checkLogin(), 5000)
    },
    logout: function () {
      this.$http.get('/api/logout').then(
        response => {
          this.$router.push({path: 'login'})
        }
      )
    },
    loadFriends: function () {
      this.$http.get('/api/friends').then(
        response => {
          this.user = response.data.friends[0]
          this.userAvatarSrc = '/api/friends/' + this.user.user_name + '/avatar'
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
    selectGroup: function (group) {
      this.selectedGroup = group
      this.groupName = group.name
      this.loadingMembers = true
      this.$http.get('/api/groups/' + this.selectedGroup.user_name + '/members').then(
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
          this.friendsInAdding = []
          this.original.friends = this.friends
          this.original.nonFriends = this.nonFriends
          this.original.friendsInAdding = this.friendsInAdding
          this.loadingMembers = false
          this.disableAdd = false
        },
        response => {
          console.error('get response error')
          console.log(response)
        }
      )
    },
    selectTab: function (tab) {
      if (tab.name === 'nonFriends') {
        this.disableAdd = false
      } else {
        this.disableAdd = true
      }
    },
    selectUser: function (user, isSelected) {
      if (isSelected) {
        this.selectedUsers.push(user)
      } else {
        this.deleteUser(this.selectedUsers, user)
      }
    },
    selectAllUsers: function (event) {
      if (this.selectAll) {
        this.nonFriends.forEach((u) => this.selectedUsers.push(u))
        this.$refs.nonFriendsBox.forEach((v) => {
          v.isSelected = true
        })
      } else {
        this.selectedUsers = []
        this.$refs.nonFriendsBox.forEach((v) => {
          v.isSelected = false
        })
      }
    },
    confirmAdd: function (event) {
      this.showDialog = true
      let [self] = this.friends.filter(
        member => member.user_name === this.user.user_name
      )
      this.verifyMessage = '我是' + self.name
    },
    addFriends: function (event) {
      this.showDialog = false
      let added = 0
      let total = this.selectedUsers.length
      let count = 0
      this.selectedUsers.forEach(user => {
        setTimeout(() => {
          added += 1
          this.addFriend(user, added, total)
        }, 5000 * count + Math.floor(Math.random() * 5 * 1000))
        count += 1
      })
    },
    addFriend: function (user, added, total) {
      this.$http.post('/api/friends', {
        'user_name': user.user_name,
        'verify_msg': this.verifyMessage
      }).then(
        response => {
          this.deleteUser(this.nonFriends, user)
          this.deleteUser(this.selectedUsers, user)
          this.friendsInAdding.push(user)
          this.$notify({
            title: 'OK',
            message: '[' + added + '/' + total + '] 已申请添加' + user.name,
            type: 'success'
          })
        })
    },
    deleteUser: function (array, user) {
      let index = array.findIndex((u) => u.user_name === user.user_name)
      if (index > -1) {
        array.splice(index, 1)
      }
      return array
    },
    searchFriends: function (event) {
      if (!this.searchText) {
        this.friends = this.original.friends
        this.nonFriends = this.original.nonFriends
        this.friendsInAdding = this.original.friendsInAdding
      }
      console.log(this.searchText)
      this.nonFriends = this.original.nonFriends.filter(
          member => {
            return member.name.indexOf(this.searchText) > -1
          })
      this.friends = this.original.friends.filter(
        member => {
          return member.name.indexOf(this.searchText) > -1
        })
      this.friendsInAdding = this.original.friendsInAdding.filter(
        member => {
          return member.name.indexOf(this.searchText) > -1
        })
    }
  },
  mounted () {
    this.checkLogin()
    this.loadFriends()
    this.loadGroups()
  },
  data: function () {
    return {
      logged: false,
      userAvatarSrc: '',
      user: {},
      groups: [],
      selectedGroup: {user_name: ''},
      groupName: '群成员',
      groupMembers: {},
      nonFriends: [],
      friends: [],
      friendsInAdding: [],
      loadingGroups: false,
      loadingMembers: false,
      selectedUsers: [],
      searchText: '',
      disableAdd: true,
      selectAll: false,
      showDialog: false,
      verifyMessage: '',
      original: {}
    }
  },
  components: {
    ElInput, ElForm, ElFormItem, ElDialog, ElCheckbox, userBox, ElTabPane, ElButton, groupBox
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

  .search-box {
    width: 120px;
    position: absolute;
    right: 220px;
    top: 20px;
  }

  .select-box {
    position: absolute;
    right: 50px;
    top: 20px;
  }

  .add-btn {
    margin-left: 20px !important;
  }

</style>

