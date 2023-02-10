<template>
  <v-app>
    <h1>Ola povo lindo</h1>
    <v-container>
      <v-card>
        <v-container>
          <v-text-field v-model="search" label="Buscar" placeholder="Digite o seu Pokemon"></v-text-field>

          <!-- {{ pokemons }} -->

          <v-row>
            <v-col cols="3" v-for="pokemon in filtrarPokemons" :key="pokemon.name">
              <v-card @click="show_pokemon(get_id(pokemon))">
                <v-container>
                  <v-row class="mx-0 d-flex justify-center">
                    <img
                      :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${get_id(pokemon)}.png`"
                      :alt="pokemon.name" width="80%">
                  </v-row>


                  <h2 class="text-center">{{ get_name(pokemon) }}</h2>
                </v-container>

              </v-card>


              <!-- <v-card>
                <v-img :src="pokemon.sprites.front_default" />
                <v-card-title>
                  {{ pokemon.name }}
                </v-card-title>
              </v-card> -->
            </v-col>
          </v-row>
        </v-container>
        <v-dialog v-model="abrir_dialog" width="800">

          <v-card v-if="seleciona_pokemon">
            <v-container>
              <v-row class="d-flex align-center">
                <v-col cols="4">
                  <img
                    :src="`https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/${seleciona_pokemon.id}.png`"
                    :alt="seleciona_pokemon.name" width="80%">
                </v-col>
                <v-col cols="8">
                  <h1>{{ get_name(seleciona_pokemon) }}</h1>
                  <ul class="d-flex pl-0">
                    <li class="d-flex ml-1" v-for="tipo in seleciona_pokemon.types" :key="tipo.type.name">

                      <v-chip label>
                        {{ tipo.type.name }}

                      </v-chip>
                    </li>

                  </ul>
                  <v-divider></v-divider>
                  <v-chip>Altura: {{ seleciona_pokemon.height * 2.54 }} cm</v-chip>
                  <v-chip>Peso: {{(seleciona_pokemon.weight / 2.205).toFixed(0) }} Kg</v-chip>



                  <h2>Moves</h2>
                  <v-simple-table>
                    <template >
                      <thead>
                        <tr>
                          <th class="text-left">
                            Level
                          </th>
                          <th class="text-left">
                            Name
                          </th>
                        </tr>
                      </thead>
                      <tbody>
                        <tr v-for="item in filtrar_moves(seleciona_pokemon)" :key="item.move.name">
                          <td>0</td>
                          <td>{{ item.move.name }}</td>
                        </tr>
                      </tbody>
                    </template>
                  </v-simple-table>


                  <h2></h2>
                  <h2>Experiencia: {{ seleciona_pokemon.base_experience }}</h2>

                  <h2>Habilidades</h2>
                  <ul>
                    <li v-for="habilidade in seleciona_pokemon.abilities" :key="habilidade.ability.name">
                      {{ habilidade.ability.name }}
                    </li>
                  </ul>





                </v-col>
              </v-row>
              <!-- {{ seleciona_pokemon }}
            -->

            </v-container>



          </v-card>
        </v-dialog>




      </v-card>
    </v-container>


  </v-app>
</template>

<script>

import axios from 'axios';


export default {
  name: 'App',

  components: {

  },




  data() {
    return {
      pokemons: [],
      search: '',
      abrir_dialog: false,
      seleciona_pokemon: null,
    }
  },



  mounted() {
    axios.get("https://pokeapi.co/api/v2/pokemon?limit=151").then((resposta) => {
      this.pokemons = resposta.data.results
    })
  },
  methods: {
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
        for(let version of item.version_group_details){
          if(version.version_group == "sword-shield"){
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
