<template>
  <div id="app" class="container">
    <div class="search-container">
      <input type="text" v-model="searchQuery" @input="searchGames" placeholder="Search games..." />
    </div>
    <div class="category-selector">
        <h1>Categories</h1>
        <button v-for="category in categories" :key="category" @click="setCategory(category)">
          {{ category }}
        </button>
    </div>
    <div class="game-list">
      <h1>Games</h1>
      <ul>
        <li v-for="game in filteredGames" :key="game.id" @click="selectGame(game)">
          {{ game.name }}
        </li>
      </ul>
    </div>
    <div class="main-content" v-if="selectedGame">
      <div class="Content">
        <div class="main-content-title">
          <h2>{{ selectedGame.name }}</h2>
          <img :src="selectedGame.image" alt="Game cover" class="game-cover" />
        </div>
        <div class="main-content-content">
          <p>{{ selectedGame.description }}</p>
          <p>URL: <a :href="selectedGame.url" target="_blank">{{ selectedGame.url }}</a></p>
          <p>Genre: {{ selectedGame.genre }}</p>
          <p>Platform: {{ selectedGame.platform }}</p>
          <p>Publisher: {{ selectedGame.publisher }}</p>
          <p>Developer: {{ selectedGame.developer }}</p>
          <p>Date: {{ selectedGame.date }}</p>
        </div>
      </div>
    </div>

    <div class="main-content" v-else>
      <h2>Welcome</h2>
      <p>Select a game to view its details.</p>
    </div>
  </div>
</template>
<script>
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      games: [],
      selectedGame: null,
      currentCategory: null,
      categories: [
        'shooter', 'strategy', 'moba', 'racing', 'sports', 'social',
        'sandbox', 'open-world', 'survival', 'pvp', 'pve', 'pixel', 'voxel',
        'zombie', 'turn-based', 'first-person', 'third-Person', 'top-down',
        'tank', 'space', 'sailing', 'side-scroller', 'superhero', 'permadeath',
        'card', 'battle-royale', 'mmo', 'mmofps', 'mmotps', '3d', '2d', 'anime',
        'fantasy', 'sci-fi', 'fighting', 'action-rpg', 'action', 'military',
        'martial-arts', 'flight', 'lowj-spec', 'tower-defense', 'horror', 'mmorts', 'mmorpg', ''
      ],
      searchQuery: '',
      filteredGames: [],
    };
  },
  methods: {
    async fetchGames(category) {
      const options = {
        method: 'GET',
        url: 'https://free-to-play-games-database.p.rapidapi.com/api/filter',
        params: { tag: category },
        headers: {
          'X-RapidAPI-Key': '6a308b9d12msh4e276d6fecfde35p1d4559jsn7f5a98e751ec',
          'X-RapidAPI-Host': 'free-to-play-games-database.p.rapidapi.com'
        }
      };
      try {
        const response = await axios.request(options);
        this.games = response.data.map(game => ({
          id: game.id,
          name: game.title,
          description: game.short_description,
          image: game.thumbnail,
          url: game.game_url,
          developer: game.developer,
          platform: game.platform,
          date: game.release_date,
          genre: game.genre,
          publisher: game.publisher

        }));
      } catch (error) {
        console.error('Fehler beim Laden der Spiele:', error);
      }
    },
    selectGame(game) {
      this.selectedGame = game;
      console.log('Selected game description:', game.description);
    },
    setCategory(category) {
      this.currentCategory = category;
      this.fetchGames(category).then(() => {
        this.filteredGames = this.games;
      });
    },
    searchGames() {
      if (this.searchQuery && Array.isArray(this.games)) { // Check if games is an array
        // Filter the games based on the search query
        this.filteredGames = this.games.filter(game =>
          game.name.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      } else {
        // If no search query, or games is not an array, reset the filteredGames to all games
        this.filteredGames = this.games || []; // Use an empty array if this.games is undefined ..
      }
    },
  },
  created() {
    this.games = []; // Initialize games as an empty array
  },
};
</script>
<style>
@import './Styles/Style.css';
</style>