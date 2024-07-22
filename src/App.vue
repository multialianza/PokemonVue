<template>
    <div id="app">
        <img src="./assets/qssp.aa4be5dd.png" alt="" class="logoPokemon">
        <!-- Muestra el número de Pokémones que han sido descubiertos -->
        <p>Pokémones descubiertos: <span class="capturados">{{ pokemonesCapturados }}</span></p>
        <div class="card">
            <PokemonCard v-for="pokemon in pokemons" :key="pokemon.id" :pokemon="pokemon"
                @pokemonCapturado="numeroDescubiertos" />
        </div>
    </div>
</template>
<script>
import axios from "axios";
import PokemonCard from "./components/PokemonCard.vue";

export default {
    name: "App",
    components: {
        PokemonCard,
    },
    data() {
        return {
            // Lista que almacenara los datos de pokémon//
            pokemons: [],
            // Contador de pokémones descubiertos //
            pokemonesCapturados: 0,
        };
    },
    // Se ejecuta cuando el componente se monta y llama a getPokemons //
    mounted() {
        this.getPokemons();
    },
    methods: {
        async getPokemons() {
            try {
                const URL_BASE = "https://pokeapi.co/api/v2/pokemon/?limit=100";
                // Realiza una solicitud a la API //
                const responseApi = await axios.get(URL_BASE);
                // Obtiene los resultados de la API //
                const allPokemons = responseApi.data.results;

                // Permite sortear 20 Pokémones aleatorios //
                const shuffledPokemons = allPokemons.sort(() => 0.5 - Math.random());
                const selectedPokemons = shuffledPokemons.slice(0, 20);

                const pokemonsResponse = await Promise.all(
                    selectedPokemons.map(async (pokemon) => {
                        const { data } = await axios.get(pokemon.url);
                        return data;
                    })
                );
                // Formatea los datos obtenidos y los almacena en this pokemons //
                const formattedPokemon = pokemonsResponse.map((pokemon) => ({
                    id: pokemon.id,
                    name: pokemon.name,
                    image: pokemon.sprites.other.dream_world.front_default,
                    encontrado: false
                }));
                this.pokemons = formattedPokemon;
            } catch (error) {
                console.error("Error fetching Pokemons:", error);

            }
        },
        numeroDescubiertos(pokemonId) {
            // Busca un pokemon por su id //
            const pokemon = this.pokemons.find(p => p.id === pokemonId);
            // Verifica si el pokémon no ha sido descubierto //
            if (pokemon && !pokemon.encontrado) {
                // Marca un pokémon como descubierto //
                pokemon.encontrado = true;
                // Incrementa el contador  //
                this.pokemonesCapturados++;
            }
        },
    }
}
</script>

<style>
#app {
    text-align: center;
    font-family: Arial, Helvetica, sans-serif;
}

.capturados {
    font-size: 25px;
    color: #385AA7;
    text-shadow: 5px 5px 5px rgb(255, 230, 0);
    font-weight: bold;
}

.card {
    display: grid;
    grid-template-columns: auto auto auto auto;
    gap: 20px;
}

.logoPokemon {
    width: 500px;
}
</style>