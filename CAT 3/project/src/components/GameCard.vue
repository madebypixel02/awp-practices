<template>
  <div class="card" @click="emit('select', game)" style="cursor: pointer;">
    <img :src="game.thumbnail" :alt="game.title" class="card__image" />
    <div class="card__content">
      <div class="card__header">
        <h2 class="card__title line-clamp-1">{{ game.title }}</h2>
      </div>
      <p class="card__description line-clamp-3">{{ game.short_description }}</p>
      <div class="card__footer">
        <ul class="card__genres">
          <li v-for="genre in displayedGenres" :key="genre" class="card__badge has-background-blue has-text-white">
            {{ genre }}
          </li>
        </ul>
        <span class="card__badge has-background-grey has-text-white">
          {{ new Date(game.release_date).toISOString().split('T')[0] }}
        </span>
      </div>
    </div>
  </div>
</template>

<script setup>
import { computed } from 'vue';

const props = defineProps({
  game: {
    type: Object,
    required: true
  }
});

const emit = defineEmits(['select']);

const displayedGenres = computed(() => {
  return props.game.genre.slice(0, 2);
});
</script>

<style scoped src="../assets/styles/GameCard.css"></style>