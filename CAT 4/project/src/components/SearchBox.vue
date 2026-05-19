<template>
  <input type="text" class="search-box" placeholder="Search games..." v-model="model" />
</template>

<script setup>
const model = defineModel();
</script>

<style scoped>
.search-box {
  width: 100%;
  padding: 10px 15px;
  border-radius: 4px;
  border: none;
  background-color: var(--bg-dark);
  color: var(--text-light);
  font-size: 16px;
}
</style>
