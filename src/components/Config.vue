<template lang="pug">
.config
  h2 Ajustes
  label Dificultad
  .mb-3
    .option(@click="dificulty = 'easy'" :class="{ active: dificulty == 'easy'}") Facil
    .option(@click="dificulty = 'medium'" :class="{ active: dificulty == 'medium'}") Medio
    .option(@click="dificulty = 'hard'" :class="{ active: dificulty == 'hard'}") Dificil
  
  .mb-3
    div Cantidad de minas: {{mines}}
    div Tamaño del tablero: {{size}} x {{size}}

  button(@click="newGame()") Iniciar juego
</template>

<script>

export default {
  data () {
    return {
      dificulty: 'easy'
    }
  },
  computed:{
    size () {
      if (this.dificulty === 'easy') return 10
      if (this.dificulty === 'medium') return 15
      return 20
    },
    mines () {
      if (this.dificulty === 'easy') return 10
      if (this.dificulty === 'medium') return 50
      return 200
    }
  },
  methods:{
    newGame () {
      this.$emit('initGame', {
        size: this.size,
        mines: this.mines,
      })
    }   
  }
}
</script>

<style scoped>
.config {
  background-color: white;
}

.option {
  cursor: pointer;
}

.option.active {
  font-weight: bold;
}

button {
  background-color: red;
}
</style>
