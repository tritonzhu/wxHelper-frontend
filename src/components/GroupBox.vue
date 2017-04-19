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
        this.$emit('select', this.group)
      }
    }
  }
</script>

<style>
  .group-info {
    /*height: 60px;*/
    color: #8492a6;
    padding: 5px;
    border-bottom: 1px solid darkgrey;
    display: flex;
    align-items: center;
    overflow: hidden;
    /*background-color: aliceblue;*/
  }

  .group-info:hover {
    cursor: pointer;
    color: #e5f9f2;
    background-color: #1f2d3d;
  }

  .group-name {
    font-size: 16px;
    padding-left: 5px;
  }

  .selected {
    color: #d3dce6;
    background-color: #475669 !important;
  }

</style>
