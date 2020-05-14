<template>
  <div id="app">
    <pre>
      {{ moves }}
    </pre>
  </div>
</template>

<script>
import axios from "axios";
export default {
  name: "App",
  data() {
    return {
      localhostBase: "http://localhost:8080",
      pokemonMoves: null,
      testMove: null,
      moves: [],
      takenMoves: []
    };
  },
  computed: {
    BASE_URL() {
      return this.localhostBase;
    }
  },
  mounted () {
    this.getMovesByPokemonId(1)
  },
  methods: {
    getMovesByPokemonId(pokemonId) {
      axios
      .get(`${this.BASE_URL}/pokemon/${pokemonId}/`)
      .then(response => {
        this.pokemonMoves = response.data.moves
        this.generateMoves()
        this.setMoveDetails()
      })
      .catch(error => {
        console.log(error);
        // this.errored = true
      })
    },
    setMovesDetailByMoveName(name, index) {
      axios
      .get(`${this.BASE_URL}/move/${name}`)
      .then(response => {
        this.moves[index].details = {
          accuracy: response.data.accuracy,
          category: response.data.damage_class.name,
          power: response.data.power,
          type: response.data.type.name
        }
      })
      .catch(error => {
        console.log(error);
        // this.errored = true
      })
    },
    generateMoves() {
      this.pokemonMoves.forEach(move => {
        
        const moveName = move.move.name
        let generations = []
        let takenGenerations = []

        move.version_group_details.forEach(vgd => {  
          const generation = this.getGeneration(vgd.version_group.name)

          if (!takenGenerations.includes(generation) && generation !== null) {
            let learnMethod = vgd.move_learn_method.name
            const level = vgd.level_learned_at
            
            if (learnMethod === 'level-up') {
              learnMethod = 'levelUp'
            }
            generations.push({
                generation: generation,
                method: learnMethod,
                level: level
              }
            )
            takenGenerations.push(generation)
          }
        })
        const moveDetails = {
          name: moveName,
          generations: generations
        }
        this.moves.push(moveDetails)
      })
    },
    setMoveDetails () {
      this.moves.forEach((move, index) => {
        this.setMovesDetailByMoveName(move.name, index)
      })
    },
    getGeneration (version) {
      switch (version) {
        case 'red-blue': return 1
        case 'yellow': return 1
        case 'gold-silver': return 2
        case 'crystal': return 2
        case 'ruby-sapphire': return 3
        case 'emerald': return 3
        case 'firered-leafgreen': return 3
        case 'diamond-pearl': return 4
        case 'platinum': return 4
        case 'heartgold-soulsilver': return 4
        case 'black-white': return 5
        case 'black-2-white-2': return 5
        case 'x-y': return 6
        case 'omega-ruby-alpha-sapphire': return 6
        case 'sun-moon': return 7
        case 'ultra-sun-ultra-moon': return 7
        default: return null
      }
    }

  }
};

// Moves -> Generations -> Game -> moves
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
}
</style>
