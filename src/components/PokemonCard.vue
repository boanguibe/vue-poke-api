<template>
    <div class="pokemon-card">
      <!-- Imagen del Pokémon; aplica el filtro si no ha sido descubierto -->
      <img :src="pokemon.sprite" :class="{ hidden: discovered }" alt="pokemon" />
  
      <!-- Input para que el usuario ingrese el nombre del Pokémon; sólo se muestra si no ha sido descubierto -->
      <input
        v-if="!discovered"
        v-model="guess"
        placeholder="¿Quién es este Pokémon?"
        @keyup.enter="checkGuess"
      />
  
      <!-- Botón para verificar el nombre; solo visible si el Pokémon no ha sido descubierto -->
      <button v-if="!discovered" @click="checkGuess">Descubrir</button>
  
      <!-- Nombre del Pokémon; sólo se muestra si ha sido descubierto -->
      <p v-else>{{ pokemon.name }}</p>
    </div>
  </template>
  
  <script>
  export default {
    props: ['pokemon'],
    data() {
      return {
        guess: '', // Nombre ingresado por el usuario
        discovered: false, // Estado que indica si el Pokémon ha sido descubierto
      };
    },
    methods: {
      // Verifica si el nombre ingresado es correcto; si no, muestra una alerta
      checkGuess() {
        // Convierte ambos nombres a minúsculas para hacer una comparación insensible a mayúsculas/minúsculas
        if (this.guess.toLowerCase() === this.pokemon.name.toLowerCase()) {
          this.discovered = true; // Marca el Pokémon como descubierto
          this.$emit('pokemonDiscovered'); // Emite un evento al componente padre para incrementar el contador
        } else {
          // Muestra una alerta si el nombre es incorrecto
          alert('Nombre incorrecto. ¡Inténtalo de nuevo!');
        }
      },
    },
  };
  </script>
  
  <style scoped>
  .pokemon-card {
    width: 150px;
    text-align: center;
  }
  
  /* Filtro borroso aplicado a la imagen */
  img {
    width: 100px;
    filter: blur(5px) grayscale(100%);
    transition: filter 0.3s ease; /* Transición suave para eliminar el filtro */
  }
  
  /* Remueve el filtro cuando el Pokémon es descubierto */
  img.hidden {
    filter: none;
  }
  
  input {
    margin-top: 10px;
    padding: 5px;
    text-align: center;
    width: 140px;
    font-size: 12px;
  }
  </style>
  
  