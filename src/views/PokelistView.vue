<script setup>
import Pokedex from "../components/Pokedex.vue";
</script>

<template>
  <div class="pokedex__base">
    <h1 class="pokedex__title">Pokédex</h1>
    <Pokedex generation="1" />
  </div>
</template>

<style>
@media (min-width: 1024px) {
  .pokedex {
    min-height: 100vh;
    display: flex;
  }
}
</style>
