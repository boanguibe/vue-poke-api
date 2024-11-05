<template>
  <div id="app">
    <header>
      <h1>Adivina el</h1>
      <img src="../public/International_Pokémon_logo.svg.png" alt="" srcset="">
      <p>¡Intenta descubrir todos los Pokémon!</p>

      <!-- Muestra el contador de Pokémon descubiertos de un total de 20 -->
      <p>Pokémon descubiertos: {{ discoveredCount }}/20</p>
    </header>
    
    <!-- Contenedor de tarjetas de Pokémon usando CSS Grid para organizar en filas de 5 columnas -->
    <div class="pokemon-container">
    <!-- Renderiza el componente PokemonCard para cada Pokémon en el array 'pokemons' -->  
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
      pokemons: [],// Array para almacenar los datos de los 20 Pokémon obtenidos
      discoveredCount: 0, // Contador de Pokémon descubiertos
    };
  },
  methods: {
    // Método para obtener la lista de 20 Pokémon y sus detalles desde la PokeAPI
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
    // Método que incrementa el contador de Pokémon descubiertos cuando el usuario adivina correctamente
    incrementCounter() {
      this.discoveredCount += 1;
    },
  },
  // Ejecuta el método fetchPokemons() cuando el componente se crea
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
header img{
  width: 30%;
}
</style>

