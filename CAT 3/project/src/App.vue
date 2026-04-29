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
        <CardList :games="filteredGames" @select="openEditGame" />
      </div>
    </div>

    <GameForm
      :is-open="formOpen"
      :game="selectedGame"
      @close="closeForm"
      @save="saveGame"
      @delete="deleteGame"
    />
  </div>
</template>

<script setup>
import { ref, computed } from 'vue';
import Header from './components/Header.vue';
import SearchBox from './components/SearchBox.vue';
import FilterBar from './components/FilterBar.vue';
import CardList from './components/CardList.vue';
import GameForm from './components/GameForm.vue';

const dummyGames = ref([
  {
    id: '1',
    title: 'Overwatch 2',
    thumbnail: 'https://preview.redd.it/its-crazy-were-getting-more-heroes-next-week-than-we-had-v0-eg2gyjljophg1.jpg?width=640&crop=smart&auto=webp&s=afd637ea55e77ba4dd56a52a00d35013a8cb92e9',
    short_description: 'A hero-focused first-person team shooter from Blizzard Entertainment.',
    genre: ['Shooter'],
    platform: ['PC', 'Playstation', 'Xbox'],
    developer: 'Blizzard Entertainment',
    release_date: new Date('2022-10-04'),
    popularity: 5
  },
  {
    id: '2',
    title: 'PUBG: BATTLEGROUNDS',
    thumbnail: 'https://external-preview.redd.it/playerunknowns-battlegrounds-is-now-optimized-for-4-core-v0-s2mvAnHCrp7YXMcRfSwpbhvxxacIZds4UH0FEWJEh7Q.jpg?auto=webp&s=1ee3acafab7262130c120b17b494d5f80d73f5d8',
    short_description: 'Get into the action in one of the longest running battle royale games PUBG.',
    genre: ['Shooter'],
    platform: ['PC', 'Playstation', 'Xbox'],
    developer: 'KRAFTON, Inc.',
    release_date: new Date('2022-01-12'),
    popularity: 4
  },
  {
    id: '3',
    title: 'Enlisted',
    thumbnail: 'https://cdn.akamai.steamstatic.com/steam/apps/796560/header.jpg',
    short_description: 'Get ready to command your own World War II military squad in Gaijin and Darkflow.',
    genre: ['Shooter'],
    platform: ['PC', 'Playstation'],
    developer: 'Darkflow Software',
    release_date: new Date('2021-04-08'),
    popularity: 3
  },
  {
    id: '4',
    title: 'FragPunk',
    thumbnail: 'https://static0.gamerantimages.com/wordpress/wp-content/uploads/2025/01/fragpunk-title-with-character-art-1.jpg',
    short_description: 'A free-to-play 5v5 hero shooter that uses cards',
    genre: ['Shooter'],
    platform: ['Xbox', 'Playstation'],
    developer: 'Bad Guitar Studio',
    release_date: new Date('2025-03-06'),
    popularity: 1
  },
  {
    id: '5',
    title: 'Throne And Liberty',
    thumbnail: 'https://cdn.akamai.steamstatic.com/steam/apps/2429640/header.jpg',
    short_description: 'A free-to-play multi-platform MMORPG from NCSOFT and Amazon Games.',
    genre: ['MMORPG'],
    platform: ['PC', 'Playstation'],
    developer: 'NCSOFT',
    release_date: new Date('2024-10-01'),
    popularity: 5
  },
  {
    id: '6',
    title: 'Fall Guys',
    thumbnail: 'https://static.wikia.nocookie.net/fallguysultimateknockout_gamepedia_en/images/e/ee/Mobile_Release_Keyart.png/revision/latest/scale-to-width-down/1000?cb=20240816121407',
    short_description: 'Play the most competitive massively multiplayer party royale game featuring beans.',
    genre: ['Battle Royale'],
    platform: ['PC', 'Playstation', 'Xbox', 'Switch'],
    developer: 'Mediatonic',
    release_date: new Date('2020-08-04'),
    popularity: 4
  }
]);

const searchQuery = ref('');
const sortBy = ref('Alphabetical');
const orderBy = ref('Ascending');
const genre = ref('All Genres');
const popularity = ref(5);

const formOpen = ref(false);
const selectedGame = ref(null);

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

const saveGame = (gameObject) => {
  const index = dummyGames.value.findIndex(g => g.id === gameObject.id);
  if (index !== -1) {
    dummyGames.value[index] = gameObject;
  } else {
    dummyGames.value.push(gameObject);
  }
};

const deleteGame = (id) => {
  dummyGames.value = dummyGames.value.filter(g => g.id !== id);
};

const resetFilters = () => {
  searchQuery.value = '';
  sortBy.value = 'Alphabetical';
  orderBy.value = 'Ascending';
  genre.value = 'All Genres';
  popularity.value = 5;
};

const availableGenres = computed(() => {
  const all = dummyGames.value.flatMap(g => g.genre);
  return [...new Set(all)].sort();
});

const filteredGames = computed(() => {
  const q = searchQuery.value.toLowerCase();

  let result = dummyGames.value.filter(game => {
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
</script>

<style scoped src="./assets/styles/App.css"></style>
