<template>
  <div class="block">
    <input class="block__item" type="search" placeholder="Search" @input="filterPokemon = $event.target.value">
    <ul class="list">
      <li class="list__item" v-for="pokemon in pokemonFiltered" :key="pokemon.id">
        <Pokemon :pokemonUrl="pokemon.sprites.front_default" :pokemonName="pokemon.name" :pokemonID="pokemon.id" />
      </li>
    </ul>
  </div>
</template>

<script>
const axios = require('axios');
import Pokemon from './Pokemon.vue';

export default {
  name: 'Home',
  components: {
    Pokemon
  },
  data() {
    return {
      listAllPokemon: [],
      listPokemonFiltered: [],
      filterPokemon: ''
    }
  },
  computed: {
    pokemonFiltered() {
      // Criando uma cópia do dados resgatados da requisição com spread para que ele não substitua a prop do data.
      const pokemons = [...this.listAllPokemon];
      return pokemons.filter(pokemon => {
        // Transformando esse return em um boolean e pegando os dados do input sem o case sensitive do regex ('i')
        return !!pokemon.name.match(new RegExp(this.filterPokemon, 'i'))
      })
    }
  },
  methods: {
  },
  created() {
    // Fazendo a requisição com axios
    axios.get('https://pokeapi.co/api/v2/pokemon?limit=151')
      .then(res => {
        // Enumerando os nomes dos pokemons para segunda requisição
        const pokemonsEnum = res.data.results.map(item => item.name);

        // Criando um array de promisses para ser excutada depois
        const proms = [];
        // Percorrendo o slug que é o nome do pokemon e adicionando uma nova Promisse no array de promisses
        pokemonsEnum.forEach((slug, index) => {
          proms.push(new Promise((resolve, reject) => {
            // Fazendo uma segunda requisição com axios passando o slug(o nome do pokemon)
            return axios.get(`https://pokeapi.co/api/v2/pokemon/${slug}`)
              .then(res => {
                // Atribuindo a resposta do segundo request para cada posição do pokemonEnums e chamando o resolve
                pokemonsEnum[index] = res.data;
                resolve();
              })
              .catch(err => {
                console.error(err)
                reject();
              })
          }))
        })
        // Executando todas as promessas do proms
        Promise.all(proms)
          .then(() => {
            // Atribundo o pokemonEnums que já está ordenado para o listAllPokemon que está no data
            this.listAllPokemon = pokemonsEnum;
          })
          .catch(err => {
            console.error(err)
          })
      })
      .catch(err => {
        console.error(err);
      })
  }

}
</script>

<style lang="scss" scoped>
.block {
  padding: 0;
  margin: 12px;

  .list {
    list-style-type: none;
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(299px, 1fr));
    grid-gap: 16px;
    padding: 0;

    .list__item {
      width: 299px;
      height: 174px;
      background: #FFF;
      border-radius: 5px;
      border-bottom: 1px solid #CECECE;
    }
  }

  .block__item {
    border: none;
    width: 100%;
    display: block;
    padding: 14px 0;
    margin: 0 auto;
    background-color: #EDEDED;
    border-bottom: 1px solid #9E9E9E;
    text-transform: capitalize;
    top: 0;
    position: sticky;

    &:focus {
      border: none;
      outline: none;
      background: inherit;
      border-bottom: 1px solid #9E9E9E;
      background-color: #EDEDED
    }
  }
}
</style>
