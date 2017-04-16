<template>
  <div class="group-info" :class="{ selected: isSelected }" @click="select">
      <img class="avatar" :src="avatarSrc">
      <span class="group-name">{{ group.name }}</span>
  </div>
</template>

<script>
  export default {
    name: 'group-box',
    props: ['group', 'selectedGroup'],
    computed: {
      avatarSrc: function () {
        return '/api/groups/' + this.group.user_name + '/avatar'
      },
      isSelected: function () {
        return this.group.user_name === this.selectedGroup
      }
    },
    methods: {
      select: function (event) {
        let div = event.target
        while (div.tagName.toLowerCase() !== 'div') {
          div = div.parentNode
        }
        this.$emit('select', div)
      }
    }
  }
</script>

<style>
  .group-info {
    /*height: 60px;*/
    padding: 5px;
    border-bottom: 1px solid darkgrey;
    display: flex;
    align-items: center;
    overflow: hidden;
    /*background-color: aliceblue;*/
  }

  .group-info:hover {
    cursor: pointer;
    background-color: rgba(100, 149, 237, 0.5)
  }

  .group-name {
    font-size: 16px;
    padding-left: 5px;
  }

  .selected {
    background-color: rgba(100, 149, 237, 0.9) !important;
  }
</style>
