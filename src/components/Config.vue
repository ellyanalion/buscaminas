<template lang="pug">
  div
    h2 Ajustes
    h3 {{mines/area}} Dificultad  
    br
    br
    input#dificulty( type="range" min="10" max="40" )
    span {{dificulty}} %
    br
    br
    h3 Filas
    br
    input#rows(type="number" placeholder="filas" min="10" max="1000" )
    br
    h3 Columnas
    br
    input#columns(type="number" placeholder="columnas" min="10" max="1000" )
    br
    button(@click="newGame()") Iniciar juego
</template>

<script>

export default {
  props:{
  },
  computed:{
    area () {
      return this.dataRows * this.dataColumns
    },
    dificulty () {
      return 100 * this.mines / this.area
    }
  },
  data () {
    return {
      }
  },
  methods:{
    config () {
      this.columns = document.getElementById("columns").value,
      this.rows = document.getElementById("rows").value,
      this.mines = document.getElementById("dificulty").value
      
    }   
  }
}
</script>

<style scoped>

input {
  width: 200px;
  height: 30px;
  border-radius: 8px;
  margin-bottom: 15px;
}

</style>
