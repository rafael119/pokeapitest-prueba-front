<template>
  <div id="app">
    <div class="hero is-success is-gradient is-bold">
      <div class="hero-body">
        <h1 class="title">
          <span class="has-text-warning">Pokedex</span>
        </h1>
        <span class="subtitle">Pokemon</span>
        <br>
        <div class="field has-addons is-pulled-right">
          <div class="control">
            <input
              v-model="searchText"
              type="text"
              class="input is-rounded"
              v-on:keyup.enter="searchData"
              placeholder="Search"
            >
          </div>
        </div>
      </div>
    </div>
    <!--
    <div class="container">
      <div class="columns is-desktop is-mobile is-tablet is-multiline is-centered">
        <pokemon
          @showModal="showModal"
          v-for="(poke, index) of pokemon"
          v-bind:key="poke.url"
          v-bind:poke="poke"
          v-bind:index="index+1"
        />
      </div>

      
    </div>-->
    <div class="container level-right">
      <nav class="pagination" role="navegation" aria-label="pagination">
        <ul class="pagination-list">
          <li>
            <a class="pagination-previous" v-on:click=" changePage2(offset - 20)">Anterior</a>
          </li>
          <li>
            <a class="pagination-link is-current">{{offset}}</a>
          </li>
          <li>
            <a class="pagination-previous" v-on:click=" changePage2(offset + 20)">Siguiente</a>
          </li>
        </ul>
      </nav>
    </div>
    <!--Tabla para pokemon-->
    <div class="container">
      <div class="notification is-centered is-info">
        <div class="table-container">
          <table class="table is-centered is-fullwidth is-striped">
            <caption>Pokemons:{{pages}}</caption>
            <thead>
              <tr>
                <th>#</th>
                <th @click="sortUsers('name')">
                  <abbr title="Nombre Pokemon">Pokémon</abbr>
                </th>
                <th>url</th>
                <th>
                  <abbr title="Ver más del pokemon">más</abbr>
                </th>
                <th>
                  <abbr title="Seleccionar">selc</abbr>
                </th>
              </tr>
            </thead>
            <tbody>
              <tr v-for="poke of searchPokemon" v-bind:key="poke.url">
                <th v-text="idpoke(poke.url)"></th>
                <th>{{poke.name}}</th>
                <th>{{poke.url}}</th>
                <th>
                  <button
                    class="button is-small is-primary is-rounded"
                    @click="showModal(poke.name);"
                  >ver mas</button>
                </th>
                <th>
                  <input
                    type="checkbox"
                    :id="poke.name"
                    :value="poke.name"
                    v-model="selecpoke"
                    :disabled="selecpoke.length>= max && selecpoke.indexOf(poke.name) == -1"
                  >
                </th>
              </tr>
            </tbody>
            <caption>
              <button
                class="button is-primary is-rounded"
                @click="showModalPoke();"
              >Ver pokemon seleccionados</button>
            </caption>
          </table>
          <!--end tabla-->
        </div>
        <!--end table-conteiner -->
      </div>
      <!--end notification-->
    </div>
    <!--end container-->
    <div class="container level-right">
      <nav class="pagination" role="navegation" aria-label="pagination">
        <ul class="pagination-list">
          <li>
            <a class="pagination-previous" v-on:click=" changePage2(offset - 20)">Anterior</a>
          </li>
          <li>
            <a class="pagination-link is-current">{{offset}}</a>
          </li>
          <li>
            <a class="pagination-previous" v-on:click=" changePage2(offset + 20)">Siguiente</a>
          </li>
        </ul>
      </nav>
    </div>
    <!--End tabla-->
    <!--Modal para mostrar mas info de pokemon-->
    <div class="modal" :class="{ 'is-active':modal}" v-if="modal">
      <div class="modal-background" @click="modal=false"></div>
      <div class="modal-card">
        <header class="modal-card-head has-background-danger">
          <p class="modal-card-title">Acerca de {{currentpokemon.name}}</p>
        </header>
        <div class="modal-card-body">
          <div class="media">
            <figure class="figure" v-for="ima in currentpokemon.sprites" v-bind:key="ima">
              <img class="is-rounded" :src="ima">
            </figure>
            <div class="media-content">
              <div class="content">
                <ul>
                  <h4>Habilidades</h4>
                  <li
                    v-for="(ability, indexo) in currentpokemon.abilities"
                    v-bind:key="ability.name"
                  >
                    <p>habilidad {{indexo+1}}: {{ability.ability.name}}</p>
                  </li>
                </ul>
                <ul>
                  <h4>Tipo</h4>
                  <li v-for="type in currentpokemon.types" v-bind:key="type.name">{{type.type.name}}</li>
                </ul>
                <h4>Height:</h4>
                <h6 v-text="currentpokemon.height"></h6>
                <h4>Weight:</h4>
                <h6 v-text="currentpokemon.weight"></h6>
              </div>
            </div>
          </div>
        </div>
        <footer class="modal-card-foot has-background-danger"></footer>
      </div>
      <button class="modal-close is-large" aria-label="close" @click="modal=false"></button>
    </div>
    <!--End modal-->
    <!--New modal    -->
    <div class="modal" :class="{ 'is-active':modal1}" v-if="modal1">
      <div class="modal-background" @click="modal1=false"></div>
      <div class="modal-card">
        <header class="modal-card-head">
          <p class="modal-card-title">Pokemones seleccionados</p>
          <button class="delete" aria-label="close" @click="modal1=false"></button>
        </header>
        <section class="modal-card-body">
          <ul>
            <li v-for="poke in selecpoke" v-bind:key="poke">{{poke}}</li>
          </ul>
        </section>
        <footer class="modal-card-foot">
          <button class="button is-success" @click="modal1=false">Save changes</button>
          <button class="button" @click="modal1=false">Cancel</button>
        </footer>
      </div>
    </div>
  </div>
</template>

<script>
import axios from "axios";
import Pokemon from "./components/Pokemon";
export default {
  name: "App",
  components: {
    Pokemon
  },
  data: function() {
    return {
      pokemon: [],
      pages: 1,
      offset: 0,
      searchText: "",
      modal: false,
      modal1: false,
      currentpokemon: {},
      selecpoke: [],
      id: 0,
      max: 10
    };
  },
  created() {
    this.fetch();
  },
  methods: {
    fetch() {
      const params = {
        offset: this.offset,
        search: this.search
      };

      let result = axios
        .get("https://pokeapi.co/api/v2/pokemon", { params })
        .then(res => {
          this.pokemon = res.data.results;
          this.pages = res.data.count;
        })
        .catch(err => {
          console.log(err);
        });
    },
    fetch2() {
      let result = axios
        .get("https://pokeapi.co/api/v2/pokemon/" + this.search)
        .then(res => {
          this.pokemon = res.data.results;
          this.pages = res.data.count;
        })
        .catch(err => {
          console.log(err);
        });
    },
    changePage2(offset) {
      this.offset = offset < 0 || offset > this.pages ? this.offset : offset;
      this.fetch();
    },
    showModal(id) {
      //
      this.fetch3(id);
    },
    showModalPoke() {
      this.modal1 = true;
    },

    async fetch3(id) {
      //
      let result = await axios.get("https://pokeapi.co/api/v2/pokemon/" + id);
      this.currentpokemon = result.data;
      this.modal = true;
      console.log(this.currentpokemon, "poke");
    },
    idpoke(url) {
      const numpokedex = url.split("/");
      return numpokedex[6];
    },
    addpoke(poke, event) {
      if (event.target.checked === true) {
        if (this.selecpoke.length < 10) {
          this.selecpoke.push(poke);
        }
      }
    },
    sortUsers: function(id) {
      this.pokemon.sort(function(a, b) {
        return a[id].localeCompare(b[id]);
      });
    }
  },
  computed: {
    searchPokemon: function() {
      return this.pokemon.filter(
        poke =>
          poke.name.includes(this.searchText) ||
          poke.url.includes(this.searchText)
      );
    }
  }
};
</script>
