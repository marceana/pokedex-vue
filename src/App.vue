<template>
  <div id="app">
    <Pokemon />
  </div>
</template>

<script>
import Pokemon from "./components/PokemonView.vue";

export default {
  name: "App",
  components: {
    Pokemon,
  },
};
</script>

<style>
  body {
    margin: 0;
  }
</style>
