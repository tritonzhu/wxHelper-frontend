<template>
  <div class="friend-container">
    <div class="friend-info" :class="{ selected: isSelected }" @click="select">
      <img class="avatar" :src="avatarSrc">
      <div class="friend-text">
        <p class="friend-name">{{ user.name }}<br/>
        <i :class="{ 'icon-men': user.sex === 1, 'icon-women': user.sex === 2 }"></i>
        <span class="loc">{{ user.province }}&nbsp;{{ user.city }}</span></p>
      </div>
    </div>
  </div>
</template>

<script>
  import ElCard from 'element-ui/packages/card/src/main'
  export default {
    components: {ElCard},
    name: 'user-box',
    props: ['group', 'user', 'isFriend'],
    computed: {
      avatarSrc: function () {
        return '/api/groups/' + this.group.user_name + '/' + this.user.user_name + '/avatar'
      },
      isMen: function () {
        return this.user.sex === 1
      },
      isWomen: function () {
        return this.user.sex === 2
      }
    },
    methods: {
      select: function (event) {
        if (!this.isFriend) {
          this.isSelected = !this.isSelected
          this.$emit('select', this.user, this.isSelected)
        }
      }
    },
    data: function () {
      return {
        isSelected: false
      }
    }
  }
</script>

<style>
  .friend-container {
    float: left;
  }

  .friend-info {
    height: 60px;
    margin: 5px;
    width: 200px;
    padding: 5px;
    border: 1px solid #d1dbe5;
    border-radius: 4px;
    box-shadow: 0 2px 4px 0 rgba(0,0,0,.12), 0 0 6px 0 rgba(0,0,0,.04);
    display: flex;
    align-items: center;
    overflow: hidden;
    color: #324057;
    background-color: #ffffff;
  }

  .friend-info:hover {
    cursor: pointer;
    color: #e5f9f2 !important;
    background-color: #1f2d3d;
  }

  .friend-text {
    width: 120px;
    padding-left: 10px;
  }

  .friend-name {
    font-size: 14px;
    overflow-x: hidden;
    text-overflow: ellipsis;
    white-space: nowrap;
  }

  .icon-men {
    display: inline-block;
    vertical-align: middle;
    width: 16px;
    height: 16px;
    background: url("/static/icon-bg.png") no-repeat;
    background-position: -384px -304px;
    background-size: 487px 462px;
  }

  .icon-women {
    display: inline-block;
    vertical-align: middle;
    width: 16px;
    height: 16px;
    background: url("/static/icon-bg.png") no-repeat;
    background-position: -368px -304px;
    background-size: 487px 462px;
  }

  .loc {
    font-size: 12px;
    color: #99a9bf;
  }

  .selected {
    color: #d3dce6;
    background-color: #475669 !important;
  }

</style>
