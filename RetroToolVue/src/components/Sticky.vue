<template>
  <div draggable="true" v-on:dragstart="startDrag($event)" :id="stickyId">
    {{ stickyText }}
  </div>
</template>

<script>
  export default {
    name: 'sticky',
    props: ['stickyId', 'stickyText', 'stickyLocation'],
    methods: {
      startDrag (ev) {
        ev.dataTransfer.setData('text/plain', JSON.stringify({id: this.stickyId, stickyText: this.stickyText, oldLocation: this.stickyLocation}))
        ev.dataTransfer.effectAllowed = 'move'
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  div {
    border: 1px dashed brown;
    cursor: move;
  }
</style>
