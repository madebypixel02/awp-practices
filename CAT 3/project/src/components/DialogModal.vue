<template>
  <div class="modal-overlay" @click.self="close">
    <div class="modal-content">
      <div class="modal-header">
        <slot name="header" />
        <button class="modal-close" @click="close">&times;</button>
      </div>
      <div class="modal-body">
        <slot name="body" />
      </div>
    </div>
  </div>
</template>

<script setup>
const emit = defineEmits(['close']);

const close = () => {
  emit('close');
};
</script>

<style scoped src="../assets/styles/DialogModal.css"></style>
