<template>

  <div class="container">
    <div class="search-container ">



      <input type="text" v-model="pokeName" />
      <button @click="submitSearch">Buscar</button>


    </div>

    <div v-if="pokeInfo.id" class="number">
      {{ '#' + pokeInfo.id }}
    </div>

    <div class="title" v-if="pokeInfo.name">
      <div class="subgrid">
        <div class="emoji"><img src=""></div>
        <div class="type" v-if="pokeInfo.types">
          <v-chip :color="color" label class="mr-5 white--text" v-for="type in pokeInfo.types" :key="type.type.name">
            {{ type.type.name }}
          </v-chip>

        </div>
        <div class="name">{{ pokeInfo.name }}</div>

        <div v-if="pokeInfo.name" class="details">
          <div class="row"><span>Height</span> <span>{{ pokeInfo.height }}</span></div>
          <div class="row"><span>Weight</span> <span>{{ pokeInfo.weight }}</span></div>
          <div class="row"><span>Base Experience:</span> <span>{{ pokeInfo.base_experience }}</span></div>
        </div>
        <div class="picture"><img :src="pokeInfo.sprites.front_default" /></div>
      </div>

      <div class="stats">
        <div class="title" >Stats</div>
        <div class="graphics" v-for="stat in pokeInfo.stats" :key="stat.stat.name">
          <div class="row">
            <div class="name">{{ stat.stat.name }}</div>
            <div class="bar">
              <div class="inside" style="width: 20%"></div>
            </div>
            <div class="base"> {{ stat.base_stat }}</div>
          </div>          
          
        </div>
      </div>







      <!-- <h3>Abilities</h3>
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

            <p>{{ evolucoes.name }}</p>
            <img
              :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${evolucoes.url.split('/')[6]}.png`" /> -->




    </div>





  </div>



</template>

<style>
.container {
  display: flex;
  flex-direction: column;
  align-items: center;
  width: 100%;
  /* height: 100vh;
  background-image: url('https://www.pockettactics.com/wp-content/sites/pockettactics/2023/01/Pok%C3%A9mon-wallpapers-5.jpg');
  background-size: cover;*/
  background-color: rgba(0, 0, 0, 0.5);
  height: 100vh;
  width: 100%;


}

.search-container {
  display: flex;
  align-items: center;

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
  props: {
    type: String,
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
                      console.log(this.evolucoes.name);

                    })

                }

              })
          }
        })
        .catch(error => {
          console.log(error);
        });
    },


  },
  computed: {



  },
};



</script>
