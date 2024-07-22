<template>
    <div class="pokemon-card">
        <!-- Muestra la imagen del pokémon,inicialmente con la clase "hidden" para ser descubierto cuando se escriba correctamente su nombre -->
        <img :src="pokemonImageUrl" :class="{ hidden: !capturado }" class="pokemon-image" alt="pokemon" />
        <!-- V-if permite mostrar el input y boton en el caso que no se encuentre el pokémon-->
        <div class="input" v-if="!capturado">
            <!-- Permite ingresar la información al input mediante la tecla enter-->
            <input class="input-text" v-model="respuesta" @keyup.enter="descubrir" />
            <!-- Permite ingresar la información al input mediante click en el botón-->
            <button @click="descubrir">Descubrir</button>
            <!-- Muestra error en caso de equivocarse en el nombre del pokémon -->
            <p v-if="mostrarError" class="error">Incorrecto, ¡Intenta de nuevamente!</p>
        </div>
        <!-- Muestra el nombre del pokémon si la respuesta es correcta y se descubre el pokémon -->
        <div v-else>
            <p>{{ pokemon.name }}</p>
        </div>
    </div>
</template>

<script>
export default {
    name: 'PokemonCard',
    props: {
        // Define que tipo de propiedades espera el componente recibir, en este caso un objeto //
        pokemon: Object,
    },
    data() {
        return {
            // Guarda el nombre que se esta intentando adivinar //
            respuesta: '',
            // Indica si el pokémon fue descubierto //
            capturado: this.pokemon.capturado,
            // Estado para mostrar el mensaje de error //
            mostrarError: false,
        };
    },
    // Esta propiedad devuelve la URL con la sprites(imagen) del pokémon //
    computed: {
        pokemonImageUrl() {
            return `https://raw.githubusercontent.com/PokeAPI/sprites/master/sprites/pokemon/other/official-artwork/${this.pokemon.id}.png`;
        },
    },
    // Define los métodos para ser utilizados por el componente //
    methods: {
        // Este método permite verficar si el nombre del pokémon es adivinado //
        descubrir() {
            // Compara valor de la respuesta con la api sean iguales, sin importar si este se escribe con mayúsculas o minúsculas //
            if (this.respuesta.toLowerCase() === this.pokemon.name.toLowerCase()) {
                this.capturado = true;
                // Emite un evento al padre para indicar que el id del pokémon ha sido encontrado //
                this.$emit('pokemonCapturado', this.pokemon.id);
                // Oculta el mensaje de error si el id es correcto //
                this.mostrarError = false;
            } else {
                // O muestra el mensaje de error si es incorrecto //
                this.mostrarError = true;
                // Siempre es bueno limpiar el input //
                this.respuesta = '';
            }
        },
    },
};
</script>

<style>
.hidden {
    filter: brightness(0);
}

.pokemon-card {
    display: inline-block;
    margin: 20px;
}

.input {
    display: inline-block;
    flex-direction: column;
    width: 50%;

}

.input-text {
    width: 94%;
    margin-bottom: 10px;
}

.pokemon-image {

    width: auto;
    height: 150px;
}

.error {
    color: red;
    margin-top: 10px;
}
</style>
