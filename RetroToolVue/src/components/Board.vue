<template>
  <section>
    <table>
      <thead>
      <th></th>
      <th v-for="column in columns">
        <span contenteditable="true" v-on:blur="changeColumnLabel(column.id, $event)">{{ column.label }}</span>
        <br>
        <button v-on:click="removeColumn(column.id)">-</button>
      </th>
      <th><button v-on:click="addColumn">+</button></th>
      </thead>
      <tbody>
      <tr v-for="row in rows">
        <td>
          <button v-on:click="removeRow(row.id)">-</button>&nbsp;
          <span contenteditable="true" v-on:blur="changeRowLabel(row.id, $event)">{{ row.label }}</span>
        </td>
        <td v-for="column in columns">
        </td>
      </tr>
      </tbody>
    </table>
    <button v-on:click="addRow">+</button>

    <textarea cols="30" rows="10" v-model="actionItems" placeholder="Action Items"></textarea>
  </section>
</template>


<script>
  import _ from 'lodash'

  export default {
    name: 'board',
    data () {
      return {
        columns: [
          {
            label: 'Continue Doing',
            id: Math.random()
          },
          {
            label: 'Start Doing',
            id: Math.random()
          },
          {
            label: 'Stop Doing',
            id: Math.random()
          }
        ],
        rows: [
          {
            label: 'Engineering Practices',
            id: Math.random()
          },
          {
            label: 'Performance',
            id: Math.random()
          },
          {
            label: 'Requirements Analysis',
            id: Math.random()
          }
        ],
        actionItems: 'Action Items:'
      }
    },
    methods: {
      removeColumn: function (id) {
        this.columns = _.filter(this.columns, column => column.id !== id)
      },
      removeRow: function (id) {
        this.rows = _.filter(this.rows, row => row.id !== id)
      },
      addColumn () {
        this.columns.push({label: 'New Column', id: Math.random()})
      },
      addRow () {
        this.rows.push({label: 'New Row', id: Math.random()})
      },
      changeColumnLabel (id, ev) {
        this.columns.splice(
          _.findIndex(this.columns, column => column.id === id),
          1,
          {id: id, label: ev.target.innerText}
        )
      },
      changeRowLabel (id, ev) {
        this.rows.splice(
          _.findIndex(this.rows, row => row.id === id),
          1,
          {id: id, label: ev.target.innerText}
        )
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
  th {
    text-align: center;
  }
</style>
