<template>
  <div class="group-info" v-on:click="select">
      <img class="avatar" v-bind:src="avatarSrc">
      <span class="group-name">{{ group.name }}</span>
  </div>
</template>

<script>
  export default {
    name: 'group-box',
    props: ['group', 'selected'],
    computed: {
      avatarSrc: function () {
        return '/api/groups/' + this.group.user_name + '/avatar'
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
    },
    watch: {
      selected: function (newSelected) {
        if (this.group.user_name !== newSelected) {
          this.$el.className = 'group-info'
        } else {
          this.$el.className = 'group-info selected'
        }
      }
    }
  }
</script>

<style>
  .group-info {
    padding: 10px;
    border-bottom: 1px solid darkgrey;
    overflow-x: hidden;
    background-color: aliceblue;
  }

  .group-info:hover {
    cursor: pointer;
    background-color: rgba(100, 149, 237, 0.5)
  }

  .group-name {
    font-size: 18px;
    padding-left: 5px;
  }

  .selected {
    background-color: rgba(100, 149, 237, 0.9) !important;
  }
</style>
