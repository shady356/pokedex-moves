<template>
  <div id="app">
    foobar
  </div>
</template>

<script>

import axios from 'axios'
export default {
  name: 'App',
  data() {
    return {
      localhostBase: 'http://localhost:8080',
      networkBase: 'http://192.168.0.18:8080/',

      pokemonList: []
    }
  },
  computed: {
    BASE_URL () {
      return this.networkBase
    }
  },
  methods: {
    getPokemon(pokemonId) {
      axios.get(`${this.BASE_URL}/pokemon-form/${pokemonId}/`)
      .then(response => {
        this.pokemonList.push(response.data)
      })
      .catch(error => {
        console.log(error)
        // this.errored = true
      })
    }
  }
}
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}
</style>
