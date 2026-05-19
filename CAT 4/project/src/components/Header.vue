<template>
  <header class="header">
    <img src="https://id-provider.uoc.edu/idp/images/logo-uoc-default.png" alt="Logo" class="header__logo" />
    <h1 class="header__title">Game Database: aperez-b</h1>
  </header>
</template>

<script setup>
</script>

<style scoped>
.header {
  display: flex;
  align-items: center;
  gap: 20px;
  padding: 10px;
  height: 70px;
  overflow: hidden;
  box-shadow: 3px 2px 20px 0px var(--paper-black);
  background: var(--header-gradient);
}

.header__logo {
  height: 100%;
  object-fit: contain;
}

.header__title {
  margin: 0;
  color: var(--text-light);
  font-size: 24px;
  font-weight: 600;
}
</style>
