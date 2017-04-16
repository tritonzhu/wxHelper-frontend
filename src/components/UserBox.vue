<template>
  <div class="friend-container">
  <div class="friend-info" :class="{ selected: isSelected }" @click="select">
    <img class="avatar" :src="avatarSrc">
    <span class="friend-name">{{ user.name }}</span>
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
      clear: function () {
        return this.index % 3 === 2
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
    /*height: 60px;*/
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

  .friend-name {
    font-size: 14px;
    padding-left: 5px;
  }

  .selected {
    background-color: rgba(100, 149, 237, 0.9) !important;
  }

</style>
