<template>
  <form>
    <textarea placeholder="Your Sticky Says:" v-model="stickyText"></textarea>
    <br>
    <button v-on:click="submitSticky(newStickyLocation, $event)">Submit</button> &nbsp;
    <button v-on:click="cancelSticky(newStickyLocation, $event)">Cancel</button>
  </form>
</template>

<script>
  import shortid from 'shortid'

  export default {
    name: 'newStickyForm',
    props: ['newStickyLocation'],
    data () {
      return {
        stickyText: '',
        stickyId: shortid.generate()
      }
    },
    methods: {
      submitSticky (newStickyLocation, ev) {
        ev.stopPropagation()
        this.$parent.addSticky(newStickyLocation, this.stickyId, this.stickyText)
        this.$parent.$data.newStickyUILocation = ''
      },
      cancelSticky (newStickyLocation, ev) {
        ev.stopPropagation()
        this.$parent.$data.newStickyUILocation = ''
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>

</style>
