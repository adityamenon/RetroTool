<template>
  <div class="sticky-form-container">
    <form class="sticky-form">
      <div class="sticky-form-text-container">
        <textarea placeholder="Your Sticky Says:" v-model="stickyText"></textarea>
      </div>
      <div class="sticky-form-action-container">
        <span v-on:click="submitSticky(newStickyLocation, $event)" class="sticky-confirm"><i class="fa fa-check-circle" aria-hidden="true"></i></span>
        <span v-on:click="cancelSticky(newStickyLocation, $event)" class="sticky-cancel"><i class="fa fa-times-circle" aria-hidden="true"></i></span>
      </div>
    </form>
  </div>
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
  .sticky-form{
    display: flex;
    flex-direction: column;
    justify-content: space-around;
    align-items: stretch;
  }
  .sticky-form-text-container textarea{
    width: 100%;
    box-sizing: border-box;
  }
  .sticky-form-action-container{
    display: flex;
    justify-content: space-around;
    align-items: center;
  }
  .sticky-confirm{
    color: green;
  }
  .sticky-cancel{
    color: red;
  }
</style>
