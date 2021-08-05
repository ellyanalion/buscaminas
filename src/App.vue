<template lang="pug">
  div
    .newGame(@click="newGame()") Juego Nuevo
    #mines 400
    Config.config(@click="config()" @rows="dataRows" @columns="dataColumns" ) X
    div(v-for="item of cells")
    //- .square(:class=”{ discovered: item.state === ‘discovered’ }” @click=”evalSquare(item)”)
    //-     label(v-if=”item.state === ‘discovered”)
    //-         img(v-if=”mine === true” :src=”mina.jpg”)
    //-         span(v-if=”item.proximity) {{item.proximity}}
         

</template>

<script>
import Config from './components/Config.vue'

export default {
  components:{ Config },
  props:{
    state: {
      discovered: 'discovered',
      flag: 'flag'
    }
  },
  data () {
    return {
      cells: [],
      rows: 20,
      columns: 20,
      mines: 30,
      size: 30,
      marked: { default:[] },
      dashboard: [],
      inGame: true,
      gameStarted: false,
      dataRows: Number,
      dataColumns: Number,
      cell:{
        x: 0,
        y: 0,
        state: '',
        mine: false
      }
    }
  },
  computed:{
  },
  methods:{
    resetVars () {
      this.marked = 0,
      this.inGame = true,
      this.gameStarted = false
    },
    emptyBoard () {
      for (let c = 0; c < this.columns; c++) {
        this.dashboard.push([])
      }
    },
    putMines () {
      for (let i = 0; i < this.mines; i++) {
        let c
        let f
        do {
          c = Math.floor(Math.random() * this.columns) 
          f = Math.floor(Math.random() * this.rows)
        } while (this.dashboard[c][f]); 
        this.dashboard[c][f] = {
          value: -1 
          } 
      }
    },
    countMines() {
      for (let f = 0; f < this.rows; f++) {
        for (let c = 0; c < this.columns; c++) {
          if (!this.dashboard[c][f]) {
            let contador = 0 
            for (let i = -1; i <= 1; i++) {
              for (let j = -1; j <= 1; j++) {
                if (i == 0 && j == 0) {
                  continue
                }
                try {
                  if (this.dashboard[c + i][f + j].value == -1) {
                    contador++
                  }
                } catch (e) {
                  return console.log(e)
                }
              }
            }
            this.dashboard[c][f] = {
              value: contador
            }
          }
        }
      }
    },
    gameDashboard () {
      this.emptyBoard() 
      this.putMines() 
      this.countMines() 
    },
    htmlDashboard() {
      let pug = ""
      for (let f = 0; f < this.rows; f++) {
        pug += `tr`
        for (let c = 0; c < this.columns; c++) {
          pug += `td#cell-${c}-${f}`
        }
      }
      let tableroHTML = document.getElementById("dashboard")
      tableroHTML.innerHTML = pug //funciona con pug?
      tableroHTML.style.width = this.columns * this.size + "px"
      tableroHTML.style.height = this.rows * this.size + "px"
      tableroHTML.style.background = "slategray"
    },
    addEvents() {
      for (let f = 0; f < this.rows; f++) {
        for (let c = 0; c < this.columns; c++) {
          let cell = document.getElementById(`cell-${c}-${f}`)
          cell.addEventListener("dblclick", function() {
            this.dobleClick(cell, c, f, )
          })
          cell.addEventListener("mouseup", function() {
            this.simpleClick(cell, c, f, )
          })
        }
      }
    },
    verificarGanador() {
    },
    verificarPerdedor() {
    },
    actualizarPanelMinas() {
      let panel = document.getElementById("mines")
      panel.innerHTML = this.mines - this.marked //delete inne.html
    },
    refreshBoard() {
      for (let f = 0; f < this.rows; f++) {
        for (let c = 0; c < this.columns; c++) {
          let cell = document.getElementById(`cell-${c}-${f}`)
          if (this.dashboard[c][f].state == "discovered") {
            cell.style.boxShadow = "none"
            switch (this.dashboard[c][f].value) {
              case -1:
                cell.innerHTML = 'B'
                cell.style.color = "black"
                cell.style.background = "white"
                break;
              case 0:
                break
              default:
                cell.innerHTML = this.dashboard[c][f].value
                break;
            }
          }
          if (this.dashboard[c][f].state == "flag") {
            cell.innerHTML = 'F'
            cell.style.background = `cadetblue`
          }
          if (this.dashboard[c][f].state == undefined) {
            cell.innerHTML = ''
            cell.style.background = ``
          }
        }
      }
      this.verificarGanador()
      this.verificarPerdedor()
      this.actualizarPanelMinas()
    },
    newGame () {
      this.resetVars()
      this.htmlDashboard() 
      this.gameDashboard()
      this.addEvents() 
      this.refreshBoard() 
    },
    dobleClick(cell, c, f) {
      if (!this.inGame) {
        return
      }
      this.openArea(c, f)
      this.refreshBoard()
    },
    simpleClick(cell, c, f, me) {
      if (!this.inGame) {
        return
      }
      if (this.dashboard[c][f].state == "discovered") {
        return
      }
      switch (me.button) {
        case 0: 
          if (this.dashboard[c][f].state == "flag") { 
            break
          }
          while (!this.gameStarted && this.dashboard[c][f].value == -1) {
            this.gameDashboard()
          }
          this.dashboard[c][f].state = "discovered"
          this.gameStarted = true 
          if (this.dashboard[c][f].value == 0) { 
            this.openArea(c, f)
          }
          break;
        case 1: 
          break;
        case 2: 
          if (this.dashboard[c][f].state == "flag") {
            this.dashboard[c][f].state = undefined
            this.marked--
          } else {
            this.dashboard[c][f].state = "flag"
            this.marked++
          }
          break;
        default:
          break;
      }
      this.refreshBoard()
    },
    openArea(c, f) {
      for (let i = -1; i <= 1; i++) {
        for (let j = -1; j <= 1; j++) {
          if (i == 0 && j == 0) {
            continue
          }
          try {
            if (this.dashboard[c + i][f + j].state != "discovered") {
              if (this.dashboard[c + i][f + j].state != "flag") {
                this.dashboard[c + i][f + j].state = "discovered" 
                if (this.dashboard[c + i][f + j].value == 0) { 
                  this.openArea(c + i, f + j)
                }
              }
            }
          } catch (e) {
            return console.log(e)
          }
        }
      }
    }
  }  
}
</script>

<style scope>
  * {
    transition: all 1s;
    box-sizing: border-box;
  }

  body {
    text-align: center;
    font-family: calibri;
    margin: 0;
    padding: 0;
    user-select: none;
    background: lightsteelblue;
  }

  table {
    background: slategray;
    margin: 20px auto;
  }

  td {
    border: rgba(0, 0, 0, 0.25) 1px solid;
    text-align: center;
    font-size: 120%;
    color: white;
  }

  h3 {
    margin-bottom: 0;
  }

  button {
    margin: 10px;
    padding: 10px;
    background-color: red;
    color: black;
    border-radius: 8px;
  }

  .newGame,
  td,
  #mines,
  .config {
    box-shadow: inset 3px 3px 0 0 rgba(255, 255, 255, 0.5),
      inset -3px -3px 0 0 rgba(0, 0, 0, 0.5);
  }

  .newGame,
  #mines,
  .config {
    padding: 10px;
    margin: 10px;
  }

  .newGame {
    background: skyblue;
    cursor: pointer;
  }

  #mines {
    background: black;
    color: red;
  }

  .config {
    background: #eee;
    cursor: pointer;
  }
</style>
