<script>
import { 
    POKEMON_TYPE_COLOURS,
    POKEMON_STATS
 } from '../utils/constants.js'

const REQUEST_NUM = 8;

export default {
    props: {
        generation: {
            type: String,
            required: false,
        },
    },
    data() {
        return {
            region: '',
            pokemons: [],
            pokemonsToDisplay: [],
            fromCounter: 0,
            toCounter: REQUEST_NUM,
        };
    },
    mounted() {
        fetch(`https://pokeapi.co/api/v2/generation/${this.generation}`)
            .then((response) => response.json())
            .then((data) => {
                this.region = data.main_region.name;
                this.pokemons = this.sortPokemonsByOrder(data.pokemon_species);
                this.setNewPokemonsToDisplay();
            });
    },
    computed: {},
    watch: {
        fromCounter() {
            this.setNewPokemonsToDisplay();
        }
    },
    methods: {
        increaseCounters() {
            this.fromCounter += REQUEST_NUM;
            this.toCounter += REQUEST_NUM;
        },

        sortPokemonsByOrder(pokemons) { // TODO: improve
            return pokemons.sort((a, b) => {
                const orderA = Number(a.url.replace('https://pokeapi.co/api/v2/pokemon-species/', '').replace('/', ''));
                const orderB = Number(b.url.replace('https://pokeapi.co/api/v2/pokemon-species/', '').replace('/', ''));

                return orderA - orderB;
            });
        },

        getOfficialImageUrl(pokemon) {
            return pokemon.sprites.other['official-artwork'].front_default;
        },

        getTypeColour(type) {
            return POKEMON_TYPE_COLOURS[type];
        },

        getStatName(stat) {
            return POKEMON_STATS[stat];
        },

        toggleCard(ev) {
            const $card = ev.currentTarget.querySelector('.pokedex__pokemonInfo');
            $card.classList.toggle('is-hidden');
        },

        padNumber(num) {
            return String(num).padStart(3, '0');
        },

        async setNewPokemonsToDisplay() {
            const newPokemons = this.pokemons.slice(this.fromCounter, this.toCounter);
            newPokemons.forEach(async (pokemon, i) => {
                let pokemonData = await this.getPokemonData(pokemon);
                this.pokemonsToDisplay.push(pokemonData);
                this.pokemonsToDisplay.sort((a, b) => a.id - b.id); // TODO: improve (sorting once, not in every loop)
            })
        },

        async getPokemonData(pokemon) {
            return fetch(`https://pokeapi.co/api/v2/pokemon/${pokemon.name}`)
                .then((response) => response.json())
                .then((data) => {
                    return data;
                });
        }
    },
}
</script>
    
<template>
    <div>
        <h2>Generation: {{ generation }}</h2>
        <p>Region: {{ region }}</p>
    </div>
    <div class="pokedex__list">
        <div class="pokedex__pokemon" v-for="pokemon in pokemonsToDisplay" @click="toggleCard">
            <img class="pokedex__pokemonImg" :src="getOfficialImageUrl(pokemon)" :alt="pokemon.name + ' image'" />
            <div class="pokedex__pokemonHeader">
                <p class="pokedex__pokemonName">{{ pokemon.name }}</p>
                <p class="pokedex__pokemonNumber">#{{ padNumber(pokemon.id) }}</p>
            </div>
            <div class="pokedex__pokemonTypes">
                <p class="pokedex__pokemonType" v-for="pokemonType in pokemon.types"
                    :style="{ 'background-color': getTypeColour(pokemonType.type.name) }">
                    {{ pokemonType.type.name }}
                </p>
            </div>
            <div class="pokedex__pokemonInfo is-hidden">
                <div class="pokedex__pokemonInfoHeader">
                    <p class="pokedex__pokemonInfoName">{{ pokemon.name }}</p>
                    <p class="pokedex__pokemonInfoNumber">#{{ padNumber(pokemon.id) }}</p>
                </div>
                <div class="pokedex__pokemonInfoTypes">
                    <p class="pokedex__pokemonInfoType" v-for="pokemonType in pokemon.types"
                        :style="{ 'background-color': getTypeColour(pokemonType.type.name) }">
                        {{ pokemonType.type.name }}
                    </p>
                </div>
                <div class="pokedex__pokemonInfoStats">
                    <div class="pokedex__pokemonInfoStat" v-for="stat in pokemon.stats">
                        <p class="pokedex__pokemonInfoStatName">{{ getStatName(stat.stat.name) }}</p>
                        <p class="pokedex__pokemonInfoValue">{{ stat.base_stat }}</p>
                    </div>
                </div>
                <div class="pokedex__pokemonInfoAbilities">
                    <div class="pokedex__pokemonInfoAbility" v-for="ability in pokemon.abilities">
                        <p>{{ ability.ability.name }}</p>
                    </div>
                </div>
            </div>
        </div>
    </div>
    <button class="pokedex__showMoreBtn" @click="increaseCounters">Show more</button>
</template>
    
<style lang="scss">
.pokedex {
    &__list {
        display: flex;
        flex-wrap: wrap;
        justify-content: space-between;
        gap: 20px;
        margin-top: 40px;
    }

    &__pokemon {
        display: flex;
        flex-direction: column;
        flex-grow: 1;
        min-width: 295px;
        padding: 20px;
        background-color: #F2F2F2;
        cursor: pointer;

        &:hover {
            background-color: #E0E0E0;
        }

        &Img {
            width: 240px;
            margin: 0 auto;
        }

        &Header {
            display: flex;
            justify-content: space-between;
        }

        &Name {
            text-transform: capitalize;
            font-size: 25px;
            font-weight: bold;
        }

        &Number {
            font-size: 15px;
            font-style: italic;
            margin-top: 10px;
        }

        &Types {
            display: flex;
            column-gap: 20px;
        }

        &Type {
            width: 50%;
            padding: 3px 10px 5px 10px;
            border: 1px solid black;
            border-radius: 20px;
            text-align: center;
            color: white;
        }

        &Info {
            position: absolute;
            top: 0;
            left: 0;
            height: 100%;
            width: 100%;
            padding: 20px;
            background-color: #E0E0E0;

            &Header {
                display: flex;
                justify-content: space-between;
            }

            &Name {
                text-transform: capitalize;
                font-size: 25px;
                font-weight: bold;
            }

            &Number {
                font-size: 15px;
                font-style: italic;
                margin-top: 10px;
            }

            &Types {
                display: flex;
                column-gap: 20px;
            }

            &Type {
                width: 50%;
                padding: 3px 10px 5px 10px;
                border: 1px solid black;
                border-radius: 20px;
                text-align: center;
                color: white;
            }

            &Stats {
                display: flex;
                flex-wrap: wrap;
                justify-content: space-between;
                margin-top: 20px;
                text-transform: capitalize;
            }

            &Stat {
                width: calc(50% - 5px);

                &Name {
                    font-weight: bold;
                }
            }

            &Abilities {
                margin-top: 20px;
                text-transform: capitalize;
            }
        }
    }

    &__showMoreBtn {
        background-color: #F2F2F2;
        margin: 40px 0;
        padding: 10px 30px;
        font-weight: bolder;
        border-radius: 5px;

        &:hover {
            background-color: #E0E0E0;
        }
    }
}
</style>
    