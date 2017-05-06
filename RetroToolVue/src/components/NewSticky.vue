<template>
  <form>
    <textarea placeholder="Your Sticky Says:" v-model="stickyText"></textarea>
  </form>
</template>

<script>
  export default {
    name: 'newSticky',
    data () {
      return {
        stickyText: ''
      }
    },
    methods: {
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
</style>
