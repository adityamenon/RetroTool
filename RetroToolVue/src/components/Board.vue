<template>
  <section>
    <table>
      <thead>
      <th></th>
      <th v-for="column in columns"> {{ column }} </th>
      </thead>
      <tbody>
      <tr v-for="row in rows">
        <td>{{ row }}</td>
        <td v-for="column in columns">
        </td>
      </tr>
      </tbody>
    </table>

    <textarea cols="30" rows="10" v-model="actionItems" placeholder="Action Items"></textarea>
  </section>
</template>

<script>
  export default {
    name: 'board',
    data () {
      return {
        columns: ['Continue', 'Start Doing', 'Stop Doing'],
        rows: ['Engineering Practices', 'Performance', 'Business Requirements Analysis'],
        actionItems: 'Action Items:'
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  th, td {
    border: 1px solid black;
    padding: 20px;
  }
</style>
