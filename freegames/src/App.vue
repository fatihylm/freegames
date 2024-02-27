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
      <h2>{{ selectedGame.name }}</h2>
      <img :src="selectedGame.image" alt="Game cover" class="game-cover" />
      <p>{{ selectedGame.description }}</p>
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
        'mmorpg', 'shooter', 'strategy', 'moba', 'racing', 'sports', 'social',
        'sandbox', 'open-world', 'survival', 'pvp', 'pve', 'pixel', 'voxel',
        'zombie', 'turn-based', 'first-person', 'third-Person', 'top-down',
        'tank', 'space', 'sailing', 'side-scroller', 'superhero', 'permadeath',
        'card', 'battle-royale', 'mmo', 'mmofps', 'mmotps', '3d', '2d', 'anime',
        'fantasy', 'sci-fi', 'fighting', 'action-rpg', 'action', 'military',
        'martial-arts', 'flight', 'low-spec', 'tower-defense', 'horror', 'mmorts'
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
        }));
      } catch (error) {
        console.error('Fehler beim Laden der Spiele:', error);
      }
    },
    selectGame(game) {
      this.selectedGame = game;
    },
    setCategory(category) {
      this.currentCategory = category;
      this.fetchGames(category).then(() => {
        this.filteredGames = this.games;
      });
    },
    searchGames() {
      if (this.searchQuery) {

        this.filteredGames = this.games.filter(game =>
          game.name.toLowerCase().includes(this.searchQuery.toLowerCase())
        );
      } else {

        this.filteredGames = this.games;
      }
    },
    watch: {
      games(newGames) {
        this.filteredGames = newGames;
      }
    },
  },
  created() {
    this.setCategory('shooter');
  },
};
</script>

<style>
html,
body {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
}

.search-bar {
  position: absolute;
  right: 20px;
  top: 20px;
}

.search-bar input[type="text"] {
  padding: 10px;
  margin-bottom: 20px;
  width: 200px;
}

.search-container {
  position: fixed;
  right: 20px;
  top: 20px;
  z-index: 1000;
}

.search-container input[type="text"] {
  padding: 10px;
  border: 1px solid #ccc;
  border-radius: 5px;
}

.container {
  display: flex;
  flex-wrap: nowrap;
  margin-left: 250px;
  width: calc(100% - 250px);
  height: 100vh;
}


.category-selector {
  background-color: #333;
  padding: 20px;
  width: 250px;
  height: 100vh;
  overflow-y: auto;
  position: fixed;
  left: 0;
  top: 0;
  z-index: 100;
}

button {
  background-color: transparent;
  color: white;
  padding: 10px;
  margin-bottom: 1px;
  border: none;
  width: calc(100% - 20px);
  text-align: left;
  border-bottom: 1px solid grey;
  font-family: Arial, Helvetica, sans-serif;
  cursor: pointer;
  transition: background-color 0.3s;
}

button:hover {
  background-color: #555;
}

.game-list {
  background-color: #333;
  color: white;
  width: 250px;
  padding: 60px;
  height: 100vh;
  overflow-y: auto;
  font-family: Arial, Helvetica, sans-serif;
}

.game-list ul {
  list-style-type: none;
  padding: 0;
}

.game-list ul li {
  cursor: pointer;
  padding: 10px;
  border-bottom: 1px solid grey;
}

.game-list ul li:hover {
  background-color: #555;
}

.main-content {
  flex-grow: 1;
  padding: 20px;
}

.game-cover {
  max-width: 100%;
  height: auto;
  margin-bottom: 20px;
}

h1 {
  font-family: Arial, Helvetica, sans-serif;
  color: white;
}

h2 {
  font-family: Arial, Helvetica, sans-serif;
}

@media (max-width: 768px) {
  .category-selector {
    position: static;
    width: 100%;
    height: auto;
  }

  .container {
    margin-left: 0;
    width: 100%;
  }

  .search-bar {
    position: static;
    width: 100%;
    padding: 10px;
  }
}
</style>
