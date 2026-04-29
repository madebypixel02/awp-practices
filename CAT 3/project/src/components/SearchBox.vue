<template>
  <input type="text" class="search-box" placeholder="Search games..." v-model="model" />
</template>

<script setup>
const model = defineModel();
</script>

<style scoped src="../assets/styles/SearchBox.css"></style>