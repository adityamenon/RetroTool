<template>
  <section>
    <div>
      Connection-State is: <em id="connection-state">{{connectionState}}</em>
    </div>
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
          <button v-on:click="newStickyUI(row.id, column.id)">s</button>
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
  import * as deepstream from 'deepstream.io-client-js'
  import shortid from 'shortid'
  import NewSticky from '@/components/NewSticky'

  export default {
    name: 'board',
    components: { NewSticky },
    data () {
      return {
        name: 'board1',
        columns: [
          {
            label: 'Continue Doing',
            id: shortid.generate()
          },
          {
            label: 'Start Doing',
            id: shortid.generate()
          },
          {
            label: 'Stop Doing',
            id: shortid.generate()
          }
        ],
        rows: [
          {
            label: 'Engineering Practices',
            id: shortid.generate()
          },
          {
            label: 'Performance',
            id: shortid.generate()
          },
          {
            label: 'Requirements Analysis',
            id: shortid.generate()
          }
        ],
        actionItems: 'Action Items:',
        ds: null,
        connectionState: null
      }
    },
    methods: {
      updateBoard: function (key, newData) {
        this.record.set(key, newData)
      },
      removeColumn: function (id) {
        this.columns = _.filter(this.columns, column => column.id !== id)
        this.updateBoard('columns', this.columns)
      },
      removeRow: function (id) {
        this.rows = _.filter(this.rows, row => row.id !== id)
        this.updateBoard('rows', this.rows)
      },
      addColumn () {
        this.columns.push({label: 'New Column', id: shortid.generate()})
        this.updateBoard('columns', this.columns)
      },
      addRow () {
        this.rows.push({label: 'New Row', id: shortid.generate()})
        this.updateBoard('rows', this.rows)
      },
      changeColumnLabel (id, ev) {
        this.columns.splice(
          _.findIndex(this.columns, column => column.id === id),
          1,
          {id: id, label: ev.target.innerText}
        )
        this.updateBoard('columns', this.columns)
      },
      changeRowLabel (id, ev) {
        this.rows.splice(
          _.findIndex(this.rows, row => row.id === id),
          1,
          {id: id, label: ev.target.innerText}
        )
        this.updateBoard('rows', this.rows)
      },
      newStickyUI (rowId, columnId) {
        console.log(NewSticky)
      }
    },
    created: function () {
      this.ds = deepstream('wss://035.deepstreamhub.com?apiKey=930bf8a7-1034-4f37-9721-ac13c12eda95')
      .login()
      .on('connectionStateChanged', connectionState => {
        this.connectionState = connectionState
      })

      // Get/Set the board
      this.record = this.ds.record.getRecord('board/' + this.name)

      this.record.subscribe(values => {
        if (values.rows !== undefined) {
          this.rows = values.rows
        }
        if (values.columns !== undefined) {
          this.columns = values.columns
        }
        this.name = values.name
      })
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
