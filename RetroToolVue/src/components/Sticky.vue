<template>
  <div>
    <span>{{ stickyText }}</span>
  </div>
</template>

<script>
  export default {
    name: 'sticky',
    props: ['stickyId', 'stickyText']
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
