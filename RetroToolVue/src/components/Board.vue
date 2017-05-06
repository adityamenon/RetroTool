<template>
  <section class="retro-board-container">
  <div>
    Connection-State is: <em id="connection-state">{{connectionState}}</em>
    <span v-if="userDetails">
        {{userDetails.name}}
        <img :src="userDetails.picture" width="50" height="50">
        <button v-on:click="logout()"> Logout </button>

      </span>
    <button v-else v-on:click="login()"> Login </button>
  </div>

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
            <td
              v-for="column in columns"
              class="info-column"
              :id="tableCellId(row.id, column.id)"
            >
              <div
                v-on:dragover="handleDragOver($event)"
                v-on:dragdrop="stickyDrop(tableCellId(row.id, column.id), $event)"
                v-on:drop="stickyDrop(tableCellId(row.id, column.id), $event)"
                class="drop-zone"
              >
                <new-sticky-form
                  v-if="showNewStickyUI(row.id, column.id)"
                  :new-sticky-location="tableCellId(row.id, column.id)"></new-sticky-form>
                <button v-else v-on:click="newStickyUI(row.id, column.id)">s</button>
                <sticky
                  :sticky-id="sticky.id"
                  :sticky-text="sticky.text"
                  :sticky-location="tableCellId(row.id, column.id)"
                  :key="sticky.id"
                  v-for="sticky in stickies[tableCellId(row.id, column.id)]"
                ></sticky>
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
</template>

<script>
  import _ from 'lodash'
  import * as deepstream from 'deepstream.io-client-js'
  import shortid from 'shortid'
  import * as auth0 from 'auth0-js'
  import NewStickyForm from '@/components/NewStickyForm'
  import Sticky from '@/components/Sticky'

  export default {
    name: 'board',
    components: { NewStickyForm, Sticky },
    data () {
      return {
        auth0: new auth0.WebAuth({
          domain: 'retrotool.auth0.com',
          clientID: 'TUp4FQ7ycV7UYcLahoa-FGGjlgVwVVXQ',
          callbackURL: 'http://localhost:3000/callback'
        }),
        userDetails: false,
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
      logout: function () {
        this.userDetails = false
        this.localStorage.removeItem('userDetails')
      },
      login: function () {
        this.auth0.popup.authorize({
          connection: 'google-oauth2',
          responseType: 'token',
          redirectUri: 'http://localhost:8080/',
          scope: 'openid name email picture'
        }, (err) => {
          console.log(err)
          this.userDetails = JSON.parse(localStorage.getItem('userDetails'))
        })
      },
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
        this.updateBoard('stickies', this.stickies)
      },
      handleDragOver (ev) {
        ev.preventDefault()
      },
      stickyDrop (newLocation, ev) {
        let allOldStickies = this.stickies
        let oldStickyData = JSON.parse(ev.dataTransfer.getData('text'))

        // remove from existing location

        this.stickies = {}

        allOldStickies[oldStickyData.oldLocation] = _.filter(
          allOldStickies[oldStickyData.oldLocation],
          sticky => sticky.id !== oldStickyData.id
        )

        // add to new location
        allOldStickies[newLocation] = [{id: oldStickyData.id, text: oldStickyData.stickyText}]

        this.stickies = allOldStickies
        this.updateBoard('stickies', this.stickies)
      },
      log (foo) {
        console.log(foo)
      },
      noop () {
        return false
      },
      evnoop (ev) {
        ev.preventDefault()
        return false
      },
      startDrag (sticky, oldLocation, ev) {
        ev.dataTransfer.setData('text/plain', JSON.stringify({id: sticky.id, oldLocation}))
        ev.dataTransfer.effectAllowed = 'move'
      }
    },
    created: function () {
      this.ds = deepstream('wss://035.deepstreamhub.com?apiKey=930bf8a7-1034-4f37-9721-ac13c12eda95')
      .login()
      .on('connectionStateChanged', connectionState => {
        this.connectionState = connectionState
      })

      this.userDetails = JSON.parse(localStorage.getItem('userDetails'))

      // Get/Set the board
      this.record = this.ds.record.getRecord('board/' + this.name)

      this.record.subscribe(values => {
        if (values.rows !== undefined) {
          this.rows = values.rows
        }

        if (values.columns !== undefined) {
          this.columns = values.columns
        }

        if (values.stickies !== undefined) {
          this.stickies = values.stickies
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
  .drop-zone {
    padding: 15px;
    border: 1px solid red;
  }
</style>
