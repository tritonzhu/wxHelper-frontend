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
  export default {
    name: 'user-box',
    props: ['group', 'user'],
    computed: {
      avatarSrc: function () {
        return '/api/groups/' + this.group + '/' + this.user.user_name + '/avatar'
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
        let div = event.target
        while (div.tagName.toLowerCase() !== 'div') {
          div = div.parentNode
        }
        this.isSelected = !this.isSelected
        this.$emit('select', div, this.isSelected)
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
    border: 1px solid darkgrey;
    display: flex;
    align-items: center;
    overflow: hidden;
    background-color: aliceblue;
  }

  .friend-info:hover {
    cursor: pointer;
    background-color: rgba(100, 149, 237, 0.5)
  }

  .friend-text {
    width: 120px;
    padding-left: 10px;
  }

  .friend-text p {
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
    color: grey;
  }

  .selected {
    background-color: rgba(100, 149, 237, 0.9) !important;
  }

</style>
