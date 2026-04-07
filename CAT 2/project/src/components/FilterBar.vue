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
        <option>Shooter</option>
        <option>MMORPG</option>
        <option>Battle Royale</option>
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
const sortBy = defineModel('sortBy');
const orderBy = defineModel('orderBy');
const genre = defineModel('genre');
const popularity = defineModel('popularity');
</script>

<style scoped src="../assets/styles/FilterBar.css"></style>