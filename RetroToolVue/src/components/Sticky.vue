<template>
  <div draggable="true" v-on:dragstart="startDrag($event)" :id="stickyId">
    <span>{{ stickyText }}</span>
    <br>
    <span v-on:click="deleteSticky(stickyId)">
      <i class="fa fa-trash" aria-hidden="true"></i>
    </span>
  </div>
</template>

<script>
  export default {
    name: 'sticky',
    props: ['stickyId', 'stickyText', 'stickyLocation'],
    methods: {
      startDrag (ev) {
        ev.dataTransfer.setData('text/plain', JSON.stringify({
          id: this.stickyId,
          stickyText: this.stickyText,
          oldLocation: this.stickyLocation
        }))
        ev.dataTransfer.effectAllowed = 'move'
      },
      deleteSticky (stickyId) {
        this.$parent.deleteSticky(stickyId, this.stickyLocation)
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  div {
    cursor: move;
  }
</style>
