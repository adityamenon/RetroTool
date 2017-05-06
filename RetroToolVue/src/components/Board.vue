<template>
  <section class="retro-board-container flex">
    <div class="retro-board-wrapper flex">
      <table class="retro-board flex">
        <thead class="retro-board-header-wrapper flex">
          <tr class="retro-board-header flex">
            <th></th>
            <th v-for="column in columns"> {{ column }} </th>
          </tr>
        </thead>
        <tbody class="retro-board-body-wrapper">
          <tr class="flex" v-for="row in rows">
            <td>{{ row }}</td>
            <td v-for="column in columns"></td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="action-notes-wrapper">
      <textarea cols="30" rows="10" v-model="actionItems" placeholder="Action Items"></textarea>
    </div>
  </section>
</template>

<script>
  export default {
    name: 'board',
    data () {
      return {
        columns: ['Continue', 'Start Doing', 'Stop Doing'],
        rows: ['Engineering Practices', 'Performance, Debugging & Devops', 'Business Requirement Analysis', 'Knowledge Sharing & Contribution', 'Scrum Team Functions'],
        actionItems: 'Action Items:'
      }
    }
  }
</script>

<!-- Add "scoped" attribute to limit CSS to this component only -->
<style scoped>
  .flex{
    display: flex;
  }
  .block{
    display: block;
  }
  .i-block{
    display: inline-block;
  }
  .retro-board-container {
    width: 100%;
    display: flex;
    align-items: flex-start;
  }
  .retro-board-wrapper {
    width: 80%;
    padding: 8px;
    display: flex;
    justify-content: center;
    align-items: stretch;
  }
  .retro-board{
    width: 100%;
    display: flex;
  }
  .retro-board-header-wrapper{
  }
  .action-notes-wrapper{
    width: 20%;
    padding: 8px;
    display: flex;
    justify-content: center;
    align-items: stretch;
  }
  th,td {
    display: block;
    border: 1px solid black;
    padding: 20px;
  }
</style>
