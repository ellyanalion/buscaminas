<template lang="pug">
  div
    .newGame(@click="mode = 'config'") Juego Nuevo ({{ mode }})
    .mines-counter 
      span.mr-3 minas {{ mines }}
      span tamaño {{ size }}
    
    config(v-if="mode === 'config'" @initGame="initGame($event)")

    div(v-if="mode === 'game'")
      h1 en el juego
      .square-container(:style="{ width: size * 30 + 'px' }")
        template(v-for="item of cells")
          .square(:class="{ discovered: item.state === 'discovered' }" @click="evalSquare(item)")
            label(v-if="item.state === 'discovered'")
              // span(v-if="item.isMine === true") B
              span(v-if="item.proximity") {{item.proximity}}

         

</template>

<script>
import Config from './components/Config.vue'

class Cell {
  constructor ({
    x,
    y,
    state = 'init',
    isMine = false
  } = {}) {
    this.x = x
    this.y = y
    this.state = state
    this.isMine = isMine
  }
}

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
      mode: 'config',
      cells: [],
      size: 20,
      mines: 30
    }
  },
  methods:{
    initGame ({ size, mines }) {
      this.size = size
      this.mines = mines

      for (let x = 0; x < size; x++) {
        for (let y = 0; y < size; y++) {
          this.cells.push(new Cell({ x, y }))
        }
      }

      let minesInField = 0
      do {
        const cell = this.cells[Math.floor(Math.random() * this.cells.length)]
        if (cell.isMine) continue
        cell.isMine = true
        minesInField++
      } while (minesInField < mines)

      this.mode = 'game'
    },

    evalSquare (cell) {
      console.log(cell)
      cell.state = 'discovered'

      if (cell.isMine) {
        alert('game over')
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

  .newGame,
  td,
  .config {
    box-shadow: inset 3px 3px 0 0 rgba(255, 255, 255, 0.5),
      inset -3px -3px 0 0 rgba(0, 0, 0, 0.5);
  }

  .newGame,
  .config {
    padding: 10px;
    margin: 10px;
  }

  .newGame {
    background: skyblue;
    cursor: pointer;
  }

  .mines-counter {
    background: black;
    color: red;
    padding: 10px;
    margin: 10px;
    box-shadow: inset 3px 3px 0 0 rgba(255, 255, 255, 0.5),
      inset -3px -3px 0 0 rgba(0, 0, 0, 0.5);
  }

  button {
    cursor: pointer;
    padding: 10px;
    background-color: rgb(202, 202, 202);
    color: black;
    border-radius: 4px;
  }

  .square {
    cursor: pointer;
    display: inline-block;
    height: 30px;
    width: 30px;
    background-color: rgb(221, 221, 221);
    box-shadow: inset 3px 3px 0 0 rgba(255, 255, 255, 0.5),
      inset -3px -3px 0 0 rgba(0, 0, 0, 0.5);
    margin-bottom: -4px;
  }

  .square.discovered {
    box-shadow: none;
  }

  .mb-1{ margin-bottom: 4px;}
  .mb-2 { margin-bottom: 8px;}
  .mb-3 { margin-bottom: 12px;}

  .mr-1{ margin-right: 4px;}
  .mr-2 { margin-right: 8px;}
  .mr-3 { margin-right: 12px;}
</style>
