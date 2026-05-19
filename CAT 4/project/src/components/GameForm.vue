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
              placeholder="Short desciption of the game"
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
import { ref } from 'vue';
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

const buildFormData = () => {
  if (props.game) {
    return {
      title: props.game.title,
      developer: props.game.developer || '',
      genre: Array.isArray(props.game.genre) ? props.game.genre.join(', ') : props.game.genre,
      platform: Array.isArray(props.game.platform) ? props.game.platform.join(', ') : props.game.platform,
      release_date: props.game.release_date,
      popularity: props.game.popularity,
      thumbnail: props.game.thumbnail,
      short_description: props.game.short_description
    };
  }
  return {
    title: '',
    developer: '',
    genre: '',
    platform: '',
    release_date: '',
    popularity: 0,
    thumbnail: '',
    short_description: ''
  };
};

const formData = ref(buildFormData());
const errorMessage = ref('');

const close = () => {
  emit('close');
};

const save = () => {
  const { title, thumbnail, short_description, release_date, popularity } = formData.value;

  if (!title || !thumbnail || !short_description || !release_date || popularity === '' || popularity === null) {
    errorMessage.value = 'Please fill in all rquired fields (title, thumbnail, description, release date, popularity).';
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
    title: formData.value.title,
    developer: formData.value.developer,
    genre: genreArray,
    platform: platformArray,
    release_date: formData.value.release_date,
    popularity: Number(formData.value.popularity),
    thumbnail: formData.value.thumbnail,
    short_description: formData.value.short_description
  };

  if (props.game) {
    gameObject.id = props.game.id;
  }

  emit('save', gameObject);
  emit('close');
};

const deleteGame = () => {
  emit('delete', props.game.id);
  emit('close');
};
</script>

<style scoped>
.game-form {
  display: flex;
  flex-direction: column;
  gap: 16px;
  padding: 20px;
  background: linear-gradient(135deg, var(--bg-darker), var(--bg-dark));
  border-radius: 8px;
  border: 1px solid var(--border-dark);
  box-shadow: 0 4px 16px rgba(0, 0, 0, 0.3);
  max-width: 700px;
}

.game-form__image {
  width: 100%;
  max-height: 150px;
  object-fit: cover;
  border-radius: 6px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.4);
}

.game-form__container {
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 12px;
}

.game-form__group {
  display: flex;
  flex-direction: column;
  gap: 4px;
}

.game-form__label {
  font-weight: 600;
  font-size: 0.75rem;
  color: var(--text-medium);
  text-transform: uppercase;
  letter-spacing: 0.5px;
}

.game-form__input,
.game-form__textarea {
  padding: 6px 10px;
  background-color: var(--bg-darker);
  border: 1px solid var(--border-dark);
  border-radius: 4px;
  color: var(--text-medium);
  font-family: var(--font-family);
  font-size: 0.85rem;
  transition: all 0.3s ease;
  outline: none;
}

.game-form__input:focus,
.game-form__textarea:focus {
  border-color: var(--blue);
  background-color: rgba(33, 150, 243, 0.05);
  box-shadow: 0 0 0 2px rgba(33, 150, 243, 0.1);
}

.game-form__input::placeholder,
.game-form__textarea::placeholder {
  color: var(--text-muted);
  font-size: 0.8rem;
}

.game-form__textarea {
  resize: vertical;
  min-height: 40px;
}

.game-form__actions {
  grid-column: 1 / -1;
  display: grid;
  grid-template-columns: 1fr 1fr;
  gap: 8px;
  margin-top: 8px;
}

.game-form__btn--cancel {
  background-color: var(--danger-red);
}

.game-form__error {
  color: var(--danger-red);
  font-weight: 600;
  font-size: 0.85rem;
}
</style>
