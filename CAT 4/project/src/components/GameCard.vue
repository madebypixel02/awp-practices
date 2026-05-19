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
          <li v-for="g in displayedGenres" :key="g" class="card__badge has-background-blue has-text-white">
            {{ g }}
          </li>
        </ul>
        <span class="card__badge has-background-grey has-text-white">
          {{ game.release_date }}
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

<style scoped>
.card {
  background-color: var(--bg-dark);
  border-radius: 8px;
  overflow: hidden;
  transition: transform 0.2s;
  cursor: pointer;
  height: 100%;
  display: grid;
  grid-template-rows: 160px 1fr;
}

.card:hover {
  transform: translateY(-4px);
}

.card__image {
  width: 100%;
  object-fit: cover;
  object-position: center;
  max-height: 100%;
}

.card__content {
  padding: 16px;
  display: grid;
  grid-template-rows: auto 1fr auto;
  gap: 14px;
  height: 100%;
}

.card__header {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

.card__title {
  font-size: 20px;
  font-weight: bold;
  color: var(--text-light);
}

.card__description {
  color: var(--text-muted);
  font-size: 14px;
  line-height: 1.5;
}

.card__footer {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.card__badge {
  padding: 2px 4px;
  border-radius: 4px;
  font-size: 12px;
  font-weight: bold;
  max-height: 20px;
}

.card__genres {
  display: flex;
  flex-direction: row;
  gap: 6px;
  list-style: none;
  padding: 0;
  margin: 0;
}
</style>
