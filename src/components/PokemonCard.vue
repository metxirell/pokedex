<script>
import {
    POKEMON_TYPE_COLOURS,
    POKEMON_STATS
} from '../utils/constants.js'

export default {
    props: {
        pokemon: {
            type: Object,
            required: true,
        },
    },
    data() {
        return {};
    },
    async mounted() {},
    computed: {},
    watch: {},
    methods: {
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
            const $card = ev.currentTarget.querySelector('.pokemonCard__info');
            $card.classList.toggle('is-hidden');
        },

        padNumber(num) {
            return String(num).padStart(3, '0');
        },
    },
}
</script>
        
<template>
    <div class="pokemonCard__base" @click="toggleCard">
        <img class="pokemonCard__img" :src="getOfficialImageUrl(pokemon)" :alt="pokemon.name + ' image'" />
        <div class="pokemonCard__header">
            <p class="pokemonCard__name">{{ pokemon.name }}</p>
            <p class="pokemonCard__number">#{{ padNumber(pokemon.id) }}</p>
        </div>
        <div class="pokemonCard__types">
            <p class="pokemonCard__type" v-for="pokemonType in pokemon.types"
                :style="{ 'background-color': getTypeColour(pokemonType.type.name) }">
                {{ pokemonType.type.name }}
            </p>
        </div>
        <div class="pokemonCard__info is-hidden">
            <div class="pokemonCard__infoHeader">
                <p class="pokemonCard__infoName">{{ pokemon.name }}</p>
                <p class="pokemonCard__infoNumber">#{{ padNumber(pokemon.id) }}</p>
            </div>
            <div class="pokemonCard__infoTypes">
                <p class="pokemonCard__infoType" v-for="pokemonType in pokemon.types"
                    :style="{ 'background-color': getTypeColour(pokemonType.type.name) }">
                    {{ pokemonType.type.name }}
                </p>
            </div>
            <div class="pokemonCard__infoStats">
                <div class="pokemonCard__infoStat" v-for="stat in pokemon.stats">
                    <p class="pokemonCard__infoStatName">{{ getStatName(stat.stat.name) }}</p>
                    <p class="pokemonCard__infoValue">{{ stat.base_stat }}</p>
                </div>
            </div>
            <div class="pokemonCard__infoAbilities">
                <div class="pokemonCard__infoAbility" v-for="ability in pokemon.abilities">
                    <p>{{ ability.ability.name }}</p>
                </div>
            </div>
        </div>
    </div>
</template>
        
<style lang="scss">
.pokemonCard {
    &__base {
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
    }

    &__img {
        width: 240px;
        margin: 0 auto;
    }

    &__header {
        display: flex;
        justify-content: space-between;
    }

    &__name {
        text-transform: capitalize;
        font-size: 25px;
        font-weight: bold;
    }

    &__number {
        font-size: 15px;
        font-style: italic;
        margin-top: 10px;
    }

    &__types {
        display: flex;
        column-gap: 20px;
    }

    &__type {
        width: 50%;
        padding: 3px 10px 5px 10px;
        border: 1px solid black;
        border-radius: 20px;
        text-align: center;
        color: white;
    }

    &__info {
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
</style>
        