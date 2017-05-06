<template>
  <div draggable="true" v-on:dragstart="startDrag($event)" :id="stickyId">
    <span>{{ stickyText }}</span>
    <br>
    <button v-on:click="deleteSticky(stickyId)">Delete</button>
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
