<template>
  <DialogModal v-if="isOpen" @close="close">
    <template #header>
      {{ game ? 'Edit Game' : 'New Game' }}
    </template>
    <template #body>
      <form class="game-form" @submit.prevent>
        <img
          v-if="formData.thumbnail"
          :src="formData.thumbnail"
          :alt="formData.title"
          class="game-form__image"
        />
        <div class="game-form__container">
          <div class="game-form__group">
            <label class="game-form__label" for="gf-title">Title</label>
            <input
              id="gf-title"
              v-model="formData.title"
              class="game-form__input"
              type="text"
              placeholder="Game title"
            />
          </div>
          <div class="game-form__group">
            <label class="game-form__label" for="gf-developer">Developer</label>
            <input
              id="gf-developer"
              v-model="formData.developer"
              class="game-form__input"
              type="text"
              placeholder="Developer name"
            />
          </div>
          <div class="game-form__group">
            <label class="game-form__label" for="gf-genre">Genre (comma separated)</label>
            <input
              id="gf-genre"
              v-model="formData.genre"
              class="game-form__input"
              type="text"
              placeholder="e.g. Shooter, RPG"
            />
          </div>
          <div class="game-form__group">
            <label class="game-form__label" for="gf-platform">Platform (comma separated)</label>
            <input
              id="gf-platform"
              v-model="formData.platform"
              class="game-form__input"
              type="text"
              placeholder="e.g. PC, Xbox"
            />
          </div>
          <div class="game-form__group">
            <label class="game-form__label" for="gf-release">Release Date</label>
            <input
              id="gf-release"
              v-model="formData.release_date"
              class="game-form__input"
              type="date"
            />
          </div>
          <div class="game-form__group">
            <label class="game-form__label" for="gf-popularity">Popularity (0-5)</label>
            <input
              id="gf-popularity"
              v-model.number="formData.popularity"
              class="filter__range"
              type="range"
              min="0"
              max="5"
            />
          </div>
          <div class="game-form__group" style="grid-column: 1 / -1;">
            <label class="game-form__label" for="gf-thumbnail">Thumbnail URL</label>
            <input
              id="gf-thumbnail"
              v-model="formData.thumbnail"
              class="game-form__input"
              type="text"
              placeholder="https://..."
            />
          </div>
          <div class="game-form__group" style="grid-column: 1 / -1;">
            <label class="game-form__label" for="gf-description">Short Description</label>
            <textarea
              id="gf-description"
              v-model="formData.short_description"
              class="game-form__textarea"
              placeholder="Short description of the game"
            />
          </div>
          <p v-if="errorMessage" class="game-form__error" style="grid-column: 1 / -1;">
            {{ errorMessage }}
          </p>
          <div class="game-form__actions">
            <button type="button" class="button" @click="save">Save</button>
            <button
              v-if="game"
              type="button"
              class="button game-form__btn--cancel"
              @click="deleteGame"
            >
              Delete
            </button>
            <button
              v-else
              type="button"
              class="button game-form__btn--cancel"
              @click="close"
            >
              Cancel
            </button>
          </div>
        </div>
      </form>
    </template>
  </DialogModal>
</template>

<script setup>
import { ref, watch } from 'vue';
import DialogModal from './DialogModal.vue';

const props = defineProps({
  game: {
    type: Object,
    default: null
  },
  isOpen: {
    type: Boolean,
    default: false
  }
});

const emit = defineEmits(['close', 'save', 'delete']);

const emptyForm = () => ({
  title: '',
  developer: '',
  genre: '',
  platform: '',
  release_date: '',
  popularity: 0,
  thumbnail: '',
  short_description: ''
});

const formData = ref(emptyForm());
const errorMessage = ref('');

const gameToFormData = (game) => ({
  title: game.title,
  developer: game.developer,
  genre: Array.isArray(game.genre) ? game.genre.join(', ') : game.genre,
  platform: Array.isArray(game.platform) ? game.platform.join(', ') : game.platform,
  release_date: new Date(game.release_date).toISOString().split('T')[0],
  popularity: game.popularity,
  thumbnail: game.thumbnail,
  short_description: game.short_description
});

watch(
  () => props.isOpen,
  (newVal) => {
    if (newVal) {
      errorMessage.value = '';
      if (props.game) {
        formData.value = gameToFormData(props.game);
      } else {
        formData.value = emptyForm();
      }
    }
  }
);

watch(
  () => props.game,
  (newGame) => {
    if (props.isOpen && newGame) {
      formData.value = gameToFormData(newGame);
    }
  }
);

const close = () => {
  emit('close');
};

const save = () => {
  const { title, thumbnail, short_description, release_date, popularity } = formData.value;

  if (!title || !thumbnail || !short_description || !release_date || popularity === '' || popularity === null) {
    errorMessage.value = 'Please fill in all required fields (title, thumbnail, description, release date, popularity).';
    return;
  }

  errorMessage.value = '';

  const genreArray = formData.value.genre
    ? formData.value.genre.split(',').map(s => s.trim()).filter(s => s)
    : [];
  const platformArray = formData.value.platform
    ? formData.value.platform.split(',').map(s => s.trim()).filter(s => s)
    : [];

  const gameObject = {
    id: props.game ? props.game.id : window.crypto.randomUUID(),
    title: formData.value.title,
    developer: formData.value.developer,
    genre: genreArray,
    platform: platformArray,
    release_date: new Date(formData.value.release_date),
    popularity: Number(formData.value.popularity),
    thumbnail: formData.value.thumbnail,
    short_description: formData.value.short_description
  };

  emit('save', gameObject);
  formData.value = emptyForm();
  emit('close');
};

const deleteGame = () => {
  emit('delete', props.game.id);
  emit('close');
};
</script>

<style scoped src="../assets/styles/GameForm.css"></style>
