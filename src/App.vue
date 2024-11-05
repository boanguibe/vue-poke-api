<template>
  <div id="app">
    <h1>Adivina el Pokémon</h1>
    <p>¡Intenta descubrir todos los Pokémon!</p>
    <p>Pokémon descubiertos: {{ discoveredCount }}/20</p>
    <!-- Contenedor de Pokémon usando CSS Grid para organizarlos en filas de 5 -->
    <div class="pokemon-container">
      <PokemonCard
        v-for="(pokemon, index) in pokemons"
        :key="index"
        :pokemon="pokemon"
        @pokemonDiscovered="incrementCounter"
      />
    </div>

   
  </div>
</template>

<script>
import axios from 'axios';
import PokemonCard from './components/PokemonCard.vue';

export default {
  components: {
    PokemonCard,
  },
  data() {
    return {
      pokemons: [],
      discoveredCount: 0,
    };
  },
  methods: {
    async fetchPokemons() {
      try {
        const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=20');
        const pokemonPromises = response.data.results.map((pokemon) =>
          axios.get(pokemon.url)
        );
        const pokemonData = await Promise.all(pokemonPromises);
        this.pokemons = pokemonData.map((res) => ({
          name: res.data.name,
          sprite: res.data.sprites.front_default,
        }));
        console.log(pokemonData.map((res) => res.data.name));
      } catch (error) {
        console.error('Error al obtener los Pokémon:', error);
      }
    },
    incrementCounter() {
      this.discoveredCount += 1;
    },
  },
  created() {
    this.fetchPokemons();
  },
};
</script>

<style>
#app {
  font-family: Arial, sans-serif;
  text-align: center;
  margin-top: 20px;
}

/* Estilos de la grilla de Pokémon */
.pokemon-container {
  display: grid;
  grid-template-columns: repeat(5, 1fr); /* Divide en 5 columnas */
  gap: 20px; /* Espacio entre las tarjetas */
  justify-items: center; /* Centra las tarjetas en cada celda */
}
</style>

