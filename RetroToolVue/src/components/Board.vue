<template>
  <section class="retro-board-container">
    <div class="retro-board-wrapper">
      <table class="retro-board-table" cellspacing="0">
        <thead class="retro-board-table-head">
          <tr>
            <th class="title-column">
              <div class="add-metrix-wrapper">
                <span class="add-row" title="Add Row" v-on:click="addRow">
                  <i class="fa fa-arrow-down" aria-hidden="true"></i>
                  <i class="fa fa-plus-circle" aria-hidden="true"></i>
                </span>
                <span class="add-column" title="Add Column" v-on:click="addColumn">
                  <i class="fa fa-arrow-right" aria-hidden="true"></i>
                  <i class="fa fa-plus-circle" aria-hidden="true"></i>
                </span>
              </div>
            </th>
            <th v-for="column in columns" class="info-column">
              <div>
                <span contenteditable="true" v-on:blur="changeColumnLabel(column.id, $event)">{{column.label}}</span><br/>
                <button v-on:click="removeColumn(column.id)">-</button>
              </div>
            </th>
          </tr>
        </thead>
        <tbody class="retro-board-table-body">
          <tr v-for="row in rows">
            <td class="title-column">
              <div>
                <button v-on:click="removeRow(row.id)">-</button>&nbsp;
                <span contenteditable="true" v-on:blur="changeRowLabel(row.id, $event)">{{ row.label }}</span>
              </div>
            </td>
            <td v-for="column in columns" class="info-column">
              <div class="sticki-wrapper">
                <span title="Add your Stickie">
                  <i class="fa fa-plus-circle" aria-hidden="true"></i>
                </span>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>
    <div class="action-notes-wrapper">
      <textarea cols="30" rows="10" v-model="actionItems" placeholder="Action Items"></textarea>
    </div>
  </section>
  <!--<button v-on:click="addColumn">+</button>
  <button v-on:click="addRow">+</button>-->
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
    align-items: stretch;
  }
  .retro-board-wrapper {
    width: 80%;
    padding: 8px;
    display: flex;
    justify-content: center;
    align-items: stretch;
  }
  .retro-board-table{
    width: 100%;
    color: #fff;
    table-layout: fixed;
    box-shadow: 1px 1px 2px 1px rgba(0,0,0,0.75);
  }
  .retro-board-table-body tr td{
    border-top: 1px solid #fff;
  }
  .title-column{
    width: 15%;
  }
  .info-column{
    min-width: 200px;
  }
  tr .info-column:nth-of-type(6n + 1){
    background-color: #c1d4dd;
  }
  tr .info-column:nth-of-type(6n + 2){
    background-color: #dfd28e;
  }
  tr .info-column:nth-of-type(6n + 3){
    background-color: #c4f0a6;
  }
  tr .info-column:nth-of-type(6n + 4){
    background-color: #d48375;
  }
  tr .info-column:nth-of-type(6n + 5){
    background-color: #c8e5ea;
  }
  tr .info-column:nth-of-type(6n + 6){
    background-color: #b69697;
  }
  .action-notes-wrapper{
    width: 20%;
    padding: 8px;
    display: flex;
    justify-content: center;
    align-items: stretch;
  }
  tr{
    
  }
  th,td {
    padding: 8px 12px;
  }
  th {
    text-align: center;
  }
  .add-metrix-wrapper{
    min-height: 50px;
    display: flex;
    justify-content: space-around;
  }
  .add-row, .add-column{
    cursor: pointer;
  }
  .add-row{
    align-self: flex-end;
  }
  .add-column{
    align-self: flex-start;
  }
  .sticki-wrapper{
    display: flex;
    min-height: 75px;
    width: 100%;
    justify-content: center;
    align-items: center;
  }
</style>
