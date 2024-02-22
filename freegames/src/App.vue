<template>
  <div id="app" class="container">
    <div class="game-list">
      <h1>Free Games</h1>
      <ul>
        <li v-for="game in games" :key="game.id" @click="selectGame(game)">
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
// Importiere Axios
import axios from 'axios';

export default {
  name: 'App',
  data() {
    return {
      games: [],
      selectedGame: null,
    };
  },
  methods: {
    async fetchGames() {
      const options = {
        method: 'GET',
        url: 'https://free-to-play-games-database.p.rapidapi.com/api/filter',
        params: {
          tag: 'shooter',
          platform: 'pc'
        },
        headers: {
          'X-RapidAPI-Key': '6a308b9d12msh4e276d6fecfde35p1d4559jsn7f5a98e751ec',
          'X-RapidAPI-Host': 'free-to-play-games-database.p.rapidapi.com'
        }
      };

      try {
        const response = await axios.request(options);
        console.log(response.data);
        // Anpassung der Daten, um sie in die Vue-Komponente zu integrieren
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
  },
  created() {
    this.fetchGames();
  },
};
</script>

<style>

html, body {
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
  /* Zusätzliche Stile, falls benötigt */
}

/* Grundlegende Stile */
.container {
  display: flex;
  flex-wrap: nowrap; /* Verhindert das Umfließen der Kinder */
  margin: 0;
  padding: 0;
  width: 100%;
  height: 100%;
}


.game-list {
  background-color: #333;
  color: white;
  width: 250px;
  padding: 20px;
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


h1{
  font-family: Arial, Helvetica, sans-serif;
}
h2{
  font-family: Arial, Helvetica, sans-serif;
}

/* Responsives Design */
@media (max-width: 768px) {
  .container {
    flex-direction: column;
  }

  .game-list, .main-content {
    width: 100%;
    height: auto;
  }
}
</style>
