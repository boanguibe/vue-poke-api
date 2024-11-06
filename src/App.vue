<template>
  <div id="app">
    <header>
      <!-- Título principal de la aplicación -->
      <h1>Adivina el </h1>
      <img src="../public/International_Pokémon_logo.svg.png" alt="" srcset="">
      <p>¡Intenta descubrir todos los Pokémon!</p>

      <!-- Contador de Pokémon descubiertos de un total de 20 -->
      <p>Pokémon descubiertos: {{ discoveredCount }}/20</p>
    </header>

    <!-- Mensaje de felicitación, visible solo si todos los Pokémon han sido descubiertos -->
    <p v-if="allDiscovered">¡Felicidades! Has descubierto todos los Pokémon.</p>

    <!-- Contenedor de Pokémon usando CSS Grid para organizarlos en filas de 5 -->
    <div class="pokemon-container">
      <!-- Renderiza el componente PokemonCard para cada Pokémon en el array 'pokemons' -->
      <PokemonCard v-for="(pokemon, index) in pokemons" :key="index" :pokemon="pokemon"
        @pokemonDiscovered="incrementCounter" />
    </div>
  </div>
</template>

<script>
// Importa Axios para realizar solicitudes HTTP y el componente hijo PokemonCard
import axios from 'axios';
import PokemonCard from './components/PokemonCard.vue';

export default {
  // Declara el componente hijo
  components: {
    PokemonCard,
  },
  data() {
    return {
      pokemons: [], // Array que almacenará los datos de los 20 Pokémon obtenidos de la API
      discoveredCount: 0, // Contador de Pokémon descubiertos, inicialmente en 0
    };
  },
  computed: {
    // Computed property que retorna true si todos los Pokémon han sido descubiertos
    allDiscovered() {
      return this.discoveredCount === 20;
    }
  },
  methods: {
    // Método para obtener la lista de 20 Pokémon y sus detalles desde la API de PokeAPI
    async fetchPokemons() {
      try {
        // Solicitud a la API para obtener un arreglo con los primeros 20 Pokémon y sus URLs de detalle
        const response = await axios.get('https://pokeapi.co/api/v2/pokemon?limit=20');

        // Crea una lista de promesas para obtener los datos detallados de cada Pokémon utilizando su URL
        const pokemonPromises = response.data.results.map((pokemon) =>
          axios.get(pokemon.url) // Realiza una solicitud para cada URL en el arreglo 'results'
        );

        // Espera a que todas las promesas se resuelvan y obtiene un arreglo con los datos de cada Pokémon
        const pokemonData = await Promise.all(pokemonPromises);

        // Mapea el arreglo de datos para extraer solo el nombre y la URL de la imagen de cada Pokémon
        this.pokemons = pokemonData.map((res) => ({
          name: res.data.name, // Nombre del Pokémon
          sprite: res.data.sprites.front_default, // URL de la imagen del Pokémon
        }));

        // Consola de depuración para ver los nombres de los Pokémon obtenidos
        console.log(pokemonData.map((res) => res.data.name));
      } catch (error) {
        // Muestra en la consola si hay un error al obtener los Pokémon
        console.error('Error al obtener los Pokémon:', error);
      }
    },
    // Método que incrementa el contador de Pokémon descubiertos
    incrementCounter() {
      this.discoveredCount += 1; // Aumenta en 1 cada vez que un Pokémon es descubierto
    },
  },
  // Ciclo de vida 'created' que ejecuta el método fetchPokemons() al crear el componente
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
  grid-template-columns: repeat(5, 1fr);
  /* Divide en 5 columnas */
  gap: 20px;
  /* Espacio entre las tarjetas */
  justify-items: center;
  /* Centra las tarjetas en cada celda */
}
header img{
  width: 300px;
}
</style>
