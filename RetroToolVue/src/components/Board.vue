<template>
  <section class="app-container">
  <div class="connection-state-container">
    <div class="connection-state-wrapper">
      <span class="connection-state-text">
        Connection-State is: <em id="connection-state">{{connectionState}}</em>
      </span>
    </div>
  </div>
  <div class="retro-board-container">  
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
              <div class="info-head">
                <span contenteditable="true" v-on:blur="changeColumnLabel(column.id, $event)">{{column.label}}</span><br/>
                <span class="info-head-delete"><i class="fa fa-trash" aria-hidden="true" v-on:click="removeColumn(column.id)"></i></span>
              </div>
            </th>
          </tr>
        </thead>
        <tbody class="retro-board-table-body">
          <tr v-for="row in rows">
            <td class="title-column">
              <div class="title-column-content">
                <span contenteditable="true" v-on:blur="changeRowLabel(row.id, $event)">{{ row.label }}</span>
                <span class="title-info-delete"><i class="fa fa-trash" aria-hidden="true" v-on:click="removeRow(row.id)"></i></span>
              </div>
            </td>
            <td v-for="column in columns" class="info-column">
              <div class="info-column-content">
                <div v-for="sticky in stickies[tableCellId(row.id, column.id)]" class="sticky-content">
                  <sticky :sticky-id="sticky.id" :sticky-text="sticky.text"></sticky>
                </div>
                <transition name="fade">
                  <new-sticky-form
                    v-if="showNewStickyUI(row.id, column.id)"
                    :new-sticky-location="tableCellId(row.id, column.id)">
                  </new-sticky-form>
                  <span v-else v-on:click="newStickyUI(row.id, column.id)" title="Add Sticky here"><i class="fa fa-plus-circle" aria-hidden="true"></i></span>
                </transition>
              </div>
            </td>
          </tr>
        </tbody>
      </table>
    </div>  
      <div class="action-notes-wrapper">
        <textarea cols="30" rows="10" v-model="actionItems" placeholder="Action Items"></textarea>
      </div>
    </div>
  </section>
</template>

<script>
  import _ from 'lodash'
  import * as deepstream from 'deepstream.io-client-js'
  import shortid from 'shortid'
  import NewStickyForm from '@/components/NewStickyForm'
  import Sticky from '@/components/Sticky'

  export default {
    name: 'board',
    components: { NewStickyForm, Sticky },
    data () {
      return {
        ds: {},
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
        connectionState: null,
        newStickyUILocation: '',
        stickies: {}
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
        this.newStickyUILocation = this.tableCellId(rowId, columnId)
      },
      showNewStickyUI (rowId, columnId) {
        return this.newStickyUILocation === this.tableCellId(rowId, columnId)
      },
      tableCellId (rowId, columnId) {
        return btoa(`${rowId},${columnId}`)
      },
      addSticky (newStickyLocation, stickyId, stickyText) {
        if (!this.stickies[newStickyLocation]) this.stickies[newStickyLocation] = []
        this.stickies[newStickyLocation].push({
          id: stickyId,
          text: stickyText
        })
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
  .flex{
    display: flex;
  }
  .block{
    display: block;
  }
  .i-block{
    display: inline-block;
  }
  .app-container{
    display: flex;
    flex-direction: column;
    align-items: stretch;
  }
  .connection-state-wrapper{
    padding: 8px;
    color: #fff;
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
    background-color: #5d4c46;
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
  .title-column-content{
    min-height: 75px;
    display: flex;
    justify-content: space-between;
    align-items: center;
  }
  .info-column-content{
    min-height: 75px;
    position: relative;
    display: flex;
    flex-wrap: wrap;
    justify-content: center;
    align-items: center;
  }
  .info-column-content .sticky-content:nth-of-type(6n + 1){
    background-color: #0072bb;
  }
  .info-column-content .sticky-content:nth-of-type(6n + 2){
    background-color: #32b92d;
  }
  .info-column-content .sticky-content:nth-of-type(6n + 3){
    background-color: #ff6eb0;
  }
  .info-column-content .sticky-content:nth-of-type(6n + 4){
    background-color: #ffcb00;
  }
  .info-column-content .sticky-content:nth-of-type(6n + 5){
    background-color: #93228d;
  }
  .info-column-content .sticky-content:nth-of-type(6n + 6){
    background-color: #ff7a5a;
  }
  .sticky-content{
    margin: 2px;
    cursor: pointer;
  }
  .sticky-form-container{
    position: absolute;
    width: 100%;
    padding: 4px;
    height: calc(100% + 16px);
    top: -12px;
    background-color: #fff;
    box-shadow: 1px 1px 2px 1px rgba(0,0,0,0.75);
  }
  .fade-enter-active, .fade-leave-active {
    transition: opacity .5s;
  }
  .fade-enter, .fade-leave-to /* .fade-leave-active in <2.1.8 */ {
    opacity: 0;
  }
  .title-info-delete{
    margin-left: 8px;
  }
  .info-head{
    display: flex;
    justify-content: space-around;
  }
  .fa{
    cursor: pointer;
  }
</style>
