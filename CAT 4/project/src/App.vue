<template>
  <div id="app">
    <Header />
    <div class="container main-grid">
      <div class="main-grid__top">
        <SearchBox v-model="searchQuery" />
        <button class="button" @click="openNewGame">New game</button>
        <button class="button" @click="resetFilters">Reset Filters</button>
      </div>

      <div class="main-grid__filterbar">
        <FilterBar
          v-model:sortBy="sortBy"
          v-model:orderBy="orderBy"
          v-model:genre="genre"
          v-model:popularity="popularity"
          :genres="availableGenres"
        />
      </div>

      <div class="main-grid__cardlist">
        <p v-if="loading" class="status-message">Loaidng games...</p>
        <p v-else-if="error" class="status-message status-message--error">{{ error }}</p>
        <CardList v-else :games="filteredGames" @select="openEditGame" />
      </div>
    </div>

    <GameForm
      v-if="formOpen"
      :is-open="formOpen"
      :game="selectedGame"
      @close="closeForm"
      @save="saveGame"
      @delete="deleteGame"
    />
  </div>
</template>

<script setup>
import { ref, computed, onMounted } from 'vue';
import Header from './components/Header.vue';
import SearchBox from './components/SearchBox.vue';
import FilterBar from './components/FilterBar.vue';
import CardList from './components/CardList.vue';
import GameForm from './components/GameForm.vue';

const API_URL = 'http://localhost:3000/games';

const games = ref([]);
const searchQuery = ref('');
const sortBy = ref('Alphabetical');
const orderBy = ref('Ascending');
const genre = ref('All Genres');
const popularity = ref(5);
const formOpen = ref(false);
const selectedGame = ref(null);
const loading = ref(false);
const error = ref(null);

const availableGenres = computed(() => {
  const all = games.value.flatMap(g => g.genre);
  return [...new Set(all)].sort();
});

const filteredGames = computed(() => {
  const q = searchQuery.value.toLowerCase();

  let result = games.value.filter(game => {
    const matchesSearch = !q ||
      game.title.toLowerCase().includes(q) ||
      game.short_description.toLowerCase().includes(q) ||
      game.genre.some(g => g.toLowerCase().includes(q)) ||
      game.platform.some(p => p.toLowerCase().includes(q)) ||
      (game.developer && game.developer.toLowerCase().includes(q));

    const matchesGenre = genre.value === 'All Genres' || game.genre.includes(genre.value);
    const matchesPopularity = game.popularity <= popularity.value;

    return matchesSearch && matchesGenre && matchesPopularity;
  });

  result = [...result].sort((a, b) => {
    let cmp = 0;
    if (sortBy.value === 'Alphabetical') {
      cmp = a.title.localeCompare(b.title);
    } else if (sortBy.value === 'Release date') {
      cmp = new Date(a.release_date).getTime() - new Date(b.release_date).getTime();
    } else if (sortBy.value === 'Popularity') {
      cmp = a.popularity - b.popularity;
    }
    return orderBy.value === 'Ascending' ? cmp : -cmp;
  });

  return result;
});

const fetchGames = async () => {
  loading.value = true;
  error.value = null;
  try {
    const res = await fetch(API_URL);
    if (!res.ok) {
      throw new Error('Could not load games (status ' + res.status + ')');
    }
    games.value = await res.json();
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};

const openNewGame = () => {
  selectedGame.value = null;
  formOpen.value = true;
};

const openEditGame = (game) => {
  selectedGame.value = game;
  formOpen.value = true;
};

const closeForm = () => {
  formOpen.value = false;
  selectedGame.value = null;
};

const saveGame = async (gameObject) => {
  loading.value = true;
  error.value = null;
  try {
    if (gameObject.id) {
      const res = await fetch(API_URL + '/' + gameObject.id, {
        method: 'PUT',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(gameObject)
      });
      if (!res.ok) {
        throw new Error('Failed to update game (status ' + res.status + ')');
      }
    } else {
      const res = await fetch(API_URL, {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(gameObject)
      });
      if (!res.ok) {
        throw new Error('Eror adding game (status ' + res.status + ')');
      }
    }
    await fetchGames();
  } catch (err) {
    error.value = err.message;
    loading.value = false;
  }
};

const deleteGame = async (id) => {
  loading.value = true;
  error.value = null;
  try {
    const res = await fetch(API_URL + '/' + id, {
      method: 'DELETE'
    });
    if (!res.ok) {
      throw new Error('Failed to delete game (status ' + res.status + ')');
    }
    await fetchGames();
  } catch (err) {
    error.value = err.message;
    loading.value = false;
  }
};

const resetFilters = () => {
  searchQuery.value = '';
  sortBy.value = 'Alphabetical';
  orderBy.value = 'Ascending';
  genre.value = 'All Genres';
  popularity.value = 5;
};

onMounted(() => {
  fetchGames();
});
</script>

<style scoped>
.main-grid {
  display: grid;
  grid-template-areas:
    ". top"
    "filterbar cardlist";
  grid-template-columns: 1fr 3fr;
  gap: 40px;
  padding-top: 40px;
}

.main-grid__top {
  grid-area: top;
  display: grid;
  grid-template-columns: 1fr auto auto;
  gap: 12px;
  height: 50px;
  align-items: center;
}

.main-grid__filterbar {
  grid-area: filterbar;
}

.main-grid__cardlist {
  grid-area: cardlist;
}

.status-message {
  color: var(--text-light);
  font-size: 1.1rem;
  text-align: center;
  padding: 40px 0;
}

.status-message--error {
  color: var(--danger-red);
}
</style>
