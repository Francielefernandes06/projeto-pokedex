<template>

  <div class="container">
    <div class="search-container">



      <input type="text" v-model="pokeName" />
      <button @click="submitSearch">Buscar</button>


    </div>

    <div class="container-dados">
      <div v-if="pokeInfo.name">
        <p>Name: {{ pokeInfo.name }}</p>
        <img :src="pokeInfo.sprites.front_default" />

        <p>Height: {{ pokeInfo.height }}</p>
        <p>Weight: {{ pokeInfo.weight }}</p>
        <p>Base Experience: {{ pokeInfo.base_experience }}</p>

        <h3>Types</h3>
        <ul>
          <li v-for="type in pokeInfo.types" :key="type.type.name">{{ type.type.name }}</li>
        </ul>

        <h3>Abilities</h3>
        <ul>
          <li v-for="ability in pokeInfo.abilities" :key="ability.ability.name">
            {{ ability.ability.name }}
          </li>
        </ul>


        <h3>Stats</h3>
        <ul>
          <li v-for="stat in pokeInfo.stats" :key="stat.stat.name">
            {{ stat.stat.name }}: {{ stat.base_stat }}
          </li>
        </ul>


<div v-if="evolucoes">
        <h3>Evoluções</h3>
        <ul>
          <li>
            {{ evolucoes.name }}

            
            <img :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${evolucoes.url.split('/')[6]}.png`" />

          </li>


          
        </ul>

</div>
       




      </div>
    </div>

  </div>

</template>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  height: 100vh;
}

.search-container {
  display: flex;
  align-items: center;
  justify-content: center;
  flex-direction: column;
  padding: 20px;
}

input[type="text"] {
  padding: 10px;
  font-size: 16px;
  border-radius: 5px;
  border: 1px solid #ccc;
  margin-bottom: 10px;
}
</style>

<script>

import axios from 'axios';


export default {
  name: 'App',

  components: {

  },




  data() {
    return {
      // pokemons: [],
      // search: '',
      // abrir_dialog: false,
      // seleciona_pokemon: null,
      searchTerm: '',
      pokeName: '',
      pokeInfo: {},
      evolutionChain: [],
      evolucoes: {},
    }
  },



  mounted() {
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=151").then((resposta) => {
      this.pokemons = resposta.data.results
    })
  },
  methods: {
    async submitSearch() {
      axios
        .get(`https://pokeapi.co/api/v2/pokemon/${this.pokeName}`)
        .then(async response => {
          this.pokeInfo = response.data;

          // Verifica se a propriedade species existe
          if (this.pokeInfo.species) {
            // Faz a requisição para buscar a Evolution Chain do pokémon
            axios.get(this.pokeInfo.species.url)
              .then(response => {

                // Armazena as informações da Evolution Chain em uma variável
                this.evolutionChain = response.data.evolution_chain.url;





                // Verifica se a propriedade evolution_chain existe
                if (this.evolutionChain) {
                  // Faz a requisição para buscar as informações da Evolution Chain
                  axios.get(this.evolutionChain)
                    .then(response => {
                      // Armazena as informações da Evolution Chain em uma variável
                     
                      this.evolucoes = response.data.chain.evolves_to[0].species;
                      console.log(typeof this.evolucoes);
                      
                    })




                }

              })
          }
        })
              .catch(error => {
                console.log(error);
              });
          },



          get_id(pokemon) {
            return Number(pokemon.url.split('/')[6])
          },
          get_name(pokemon) {
            return pokemon.name.charAt(0).toUpperCase() + pokemon.name.slice(1);
          },
          show_pokemon(id) {
            axios.get(`https://pokeapi.co/api/v2/pokemon/${id}`).then((resposta) => {
              this.seleciona_pokemon = resposta.data
              this.abrir_dialog = !this.abrir_dialog
            })
          },
          filtrar_moves(pokemon) {
            return pokemon.moves.filter(item => {
              let incluir = false;
              for (let version of item.version_group_details) {
                if (version.version_group == "sword-shield") {
                  incluir = true;
                }
              }
              return incluir;
            })
          }
        },
          computed: {
          filtrarPokemons() {
            return this.pokemons.filter(item => {
              return item.name.toLowerCase().includes(this.search.toLowerCase())
            })
          }
        }


};
</script>
