<template  v-if="pokeInfo.types">


  <div style="
    gap: 2rem;" class="container text-center">



    <div class="search-container">
      <form @submit.prevent="submitSearch">
        <input type="text" v-model="pokeName">
        <button class="pesquisar" type="submit">Buscar</button>
      </form>


    </div>

    <div v-if="pokeInfo.id" class="number">
      {{ '#' + pokeInfo.id }}
    </div>

    <div class="info justify-content-evenly" v-if="pokeInfo.name">
      <div class="img-pokemon col-4"><img style="
    width: 60%;
" :src="pokeInfo.sprites.front_default" /></div>

      <div class="dados-pokemon col-4">
        <div class="type" v-if="pokeInfo.types">
          <v-chip label class="mr-5 white--text" v-for="type in pokeInfo.types" :key="type.type.name"
            :style="{ backgroundColor: getBackgroundColorByType(type.type.name) }">
            {{ type.type.name }}
          </v-chip>

        </div>

        <div class="name">{{ pokeInfo.name }}</div>


        <div v-if="pokeInfo.name" class="details">
          <div class="sub-dados justify-content-between">
            <span>Height</span> <span>{{ pokeInfo.height }}</span>
          </div>
          <div class="sub-dados justify-content-between">
            <span>Weight</span> <span>{{ pokeInfo.weight }}</span>
          </div>
          <div class="sub-dados justify-content-between">
            <span>Base Experience:</span> <span>{{ pokeInfo.base_experience }}</span>
          </div>
        </div>


      </div>


    </div>

    <div class="status-pokemon" v-if="pokeInfo.name">

      <div class="stats">
        <div class="title">Stats</div>
        <div class="graphics" v-for="stat in pokeInfo.stats" :key="stat.stat.name">
          <div class="stats-dados">
            <div class="name">{{ stat.stat.name }}</div>
            <div class="base"> {{ stat.base_stat }}</div>
          </div>

        </div>
      </div>

      <div class="evolucoes">
        <div class="dados-evolucoes" v-if="evolucoes">
          <div class="title">Evolu????o</div>

          <div class="name">{{ evolucoes.name }}</div>

          <img style="
    width: 100%;
"
            :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${evolucoes.url.split('/')[6]}.png`" />
        </div>

      </div>




    </div>

    <!-- <div class="title" v-if="pokeInfo.name">
      <div class="subgrid">
        <div class="emoji"><img src=""></div>
        <div class="type" v-if="pokeInfo.types">
          <v-chip label class="mr-5 white--text" v-for="type in pokeInfo.types" :key="type.type.name"
            :style="{ backgroundColor: getBackgroundColorByType(type.type.name) }">
            {{ type.type.name }}
          </v-chip>

        </div>
        <div class="name">{{ pokeInfo.name }}</div>

        <div v-if="pokeInfo.name" class="details">
          <div class="row"><span>Height</span> <span>{{ pokeInfo.height }}</span></div>
          <div class="row"><span>Weight</span> <span>{{ pokeInfo.weight }}</span></div>
          <div class="row"><span>Base Experience:</span> <span>{{ pokeInfo.base_experience }}</span></div>
        </div>
        <div class="img-pokemon"><img :src="pokeInfo.sprites.front_default" /></div>
      </div>


      <div class="dados">

        <div class="stats">
          <div class="title">Stats</div>
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


        <div class="evolucoes">
          <div class="row" v-if="evolucoes">
            <div class="title">Evolu????o</div>

            <div class="name">{{ evolucoes.name }}</div>

            <img
              :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${evolucoes.url.split('/')[6]}.png`" />
          </div>


        </div>

      </div>

    </div> -->


  </div>



</template>

<style>
.header {

  background-color: #DEF3FD;
  display: flex;
  justify-content: center;
  align-items: center;
  font-size: 30px;
  font-weight: bold;
  color: #000;
  text-transform: uppercase;
  margin-bottom: 20px;
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

      searchTerm: '',
      pokeName: '',
      pokeInfo: {},
      evolutionChain: [],
      evolucoes: {},

    }
  },



  mounted() {
    this.pokeInfo = {};
    // axios.get("https://pokeapi.co/api/v2/pokemon?limit=151").then((resposta) => {
    //   this.pokemons = resposta.data.results
    // })
  },
  methods: {


    async submitSearch() {
      console.log('SubmitSearch iniciado');

      this.pokeInfo = {};

      axios
        .get(`https://pokeapi.co/api/v2/pokemon/${this.pokeName.toLowerCase()}`)
        .then(async response => {
          console.log('Requisi????o 1 conclu??da');

          this.pokeInfo = response.data;

          // Verifica se a propriedade species existe
          if (this.pokeInfo.species) {
            console.log('Propriedade species existe');

            // Faz a requisi????o para buscar a Evolution Chain do pok??mon
            axios.get(this.pokeInfo.species.url)
              .then(response => {
                console.log('Requisi????o 2 conclu??da');

                // Armazena as informa????es da Evolution Chain em uma vari??vel
                this.evolutionChain = response.data.evolution_chain.url;

                // Verifica se a propriedade evolution_chain existe
                if (this.evolutionChain) {
                  console.log('Propriedade evolution_chain existe');

                  // Faz a requisi????o para buscar as informa????es da Evolution Chain
                  axios.get(this.evolutionChain)
                    .then(response => {
                      console.log('Requisi????o 3 conclu??da');

                      // Armazena as informa????es da Evolution Chain em uma vari??vel
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

    getBackgroundColorByType(type) {
      switch (type) {
        case "grass":
          return "#33a165";
        case "poison":
          return "#7e0058";
        case "water":
          return "#0050ac";
        case "bug":
          return "#6e1a00";
        case "ground":
          return "##c90086";
        case "fairy":
          return "#d31c81";
        case "fighting":
          return "#634136";
        case "fire":
          return "red";
        case "flying":
          return "#5d4e75";
        case "psychic":
          return "#c90086";
        case "electric":
          return "#bba909";
        case "rock":
          return "#6e1a00";
        case "ice":
          return "#70deff";
        case "dragon":
          return "#00c431";
        case "normal":
          return "grey";
        case "ghost":
          return "#5d4e75";
        default:
          return "grey";
      }
    }


  },
  computed: {




  },
};



</script>
