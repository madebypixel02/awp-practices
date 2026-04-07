<template>
  <main class="card-list">
    <GameCard 
      v-for="game in games" 
      :key="game.id" 
      :game="game" 
    />
  </main>
</template>

<script setup>
import GameCard from './GameCard.vue';

defineProps({
  games: {
    type: Array,
    required: true
  }
});
</script>

<style scoped src="../assets/styles/CardList.css"></style>