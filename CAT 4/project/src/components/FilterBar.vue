<template>
  <aside class="filter">
    <div class="filter__field">
      <label class="filter__label">Sort by:</label>
      <select class="filter__select" v-model="sortBy">
        <option>Alphabetical</option>
        <option>Release date</option>
        <option>Popularity</option>
      </select>
    </div>
    <div class="filter__field">
      <label class="filter__label">Order by:</label>
      <select class="filter__select" v-model="orderBy">
        <option>Ascending</option>
        <option>Descending</option>
      </select>
    </div>
    <div class="filter__field">
      <label class="filter__label">Genre:</label>
      <select class="filter__select" v-model="genre">
        <option>All Genres</option>
        <option v-for="g in genres" :key="g">{{ g }}</option>
      </select>
    </div>
    <div class="filter__field">
      <label class="filter__label">Popularity:</label>
      <input type="range" min="0" max="5" class="filter__range" v-model="popularity" />
      <span class="filter__range-value">0 - {{ popularity }}</span>
    </div>
  </aside>
</template>

<script setup>
defineProps({
  genres: {
    type: Array,
    default: () => []
  }
});

const sortBy = defineModel('sortBy');
const orderBy = defineModel('orderBy');
const genre = defineModel('genre');
const popularity = defineModel('popularity');
</script>

<style scoped>
.filter {
  background-color: var(--bg-dark);
  padding: 20px;
  border-radius: 8px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.filter__field {
  display: flex;
  flex-direction: column;
  gap: 8px;
}

.filter__label {
  color: var(--text-light);
  font-size: 14px;
  font-weight: 600;
}

.filter__select {
  background-color: var(--bg-darker);
  color: var(--text-medium);
  border: 1px solid var(--border-dark);
  border-radius: 4px;
  padding: 10px 12px;
  font-size: 14px;
  cursor: pointer;
  transition: border-color 0.2s;
}

.filter__select:hover {
  border-color: var(--badge-blue);
}

.filter__select:focus {
  outline: none;
  border-color: var(--badge-blue);
}

.filter__range {
  width: 100%;
  height: 6px;
  background: var(--bg-darker);
  border-radius: 3px;
  outline: none;
  cursor: pointer;
}

.filter__range-value {
  color: var(--badge-blue);
  font-size: 14px;
  font-weight: 600;
  text-align: center;
  display: inline-block;
  margin-top: 4px;
}
</style>
