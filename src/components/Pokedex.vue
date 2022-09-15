<script>
import PokemonCard from './PokemonCard.vue'

const REQUEST_NUM = 4;

export default {
    props: {
        generation: {
            type: String,
            required: false,
        },
    },
    data() {
        return {
            region: "",
            pokemonsList: [],
            pokemonsData: [],
        };
    },
    async mounted() {
        const pokemonsList = await this.getPokemonsList();
        this.region = pokemonsList.main_region.name;
        this.pokemonsList = this.sortPokemonsById(pokemonsList.pokemon_species);
        this.setPokemonsData();
    },
    computed: {},
    watch: {},
    methods: {
        sortPokemonsById(pokemons) {
            return pokemons.sort((a, b) => {
                const idA = this.getIdFromUrl(a.url);
                const idB = this.getIdFromUrl(b.url);
                return idA - idB;
            });
        },
        getIdFromUrl(url) {
            return Number(url.replace("https://pokeapi.co/api/v2/pokemon-species/", "").replace("/", ""));
        },
        async getPokemonsList() {
            return fetch(`https://pokeapi.co/api/v2/generation/${this.generation}`)
                .then((response) => response.json())
                .then((data) => data);
        },
        async getPokemonData(pokemon) {
            return fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon.name}`)
                .then((response) => response.json())
                .then((data) => {
                return data;
            });
        },
        async setPokemonsData() {
            const batchSize = REQUEST_NUM;
            let position = 0;
            while (position < this.pokemonsList.length) {
                const itemsForBatch = this.pokemonsList.slice(position, position + batchSize);
                const results = [...await Promise.all(itemsForBatch.map(item => this.getPokemonData(item)))];
                this.pokemonsData.push(...results);
                position += batchSize;
            }
        },
    },
    components: { PokemonCard }
}
</script>
    
<template>
    <div class="pokedex__base">
        <div class="pokedex__header">
            <h2 class="pokedex__generation">Generation: {{ generation }}</h2>
            <p class="pokedex__region">Region: {{ region }}</p>
        </div>
        <div class="pokedex__list" >
            <template v-for="pokemon in pokemonsData">
                <PokemonCard :pokemon="pokemon" />
            </template>
        </div>
    </div>
</template>
    
<style lang="scss">
.pokedex {
    &__region {
        text-transform: capitalize;
    }

    &__list {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        gap: 20px;
        margin-top: 40px;
    }
}
</style>
    