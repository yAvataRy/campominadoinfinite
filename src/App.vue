<template>
  <div
    class="bg-gray-900 text-white min-h-screen flex flex-col items-center justify-center font-sans p-4"
  >
    <!-- Título do Jogo -->
    <h1 class="text-4xl font-bold mb-4 text-emerald-400">Campo Minado</h1>

    <!-- Modal de Nome do Jogador -->
    <div
      v-if="!playerName"
      class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50"
    >
      <div class="bg-gray-800 p-8 rounded-xl shadow-2xl max-w-md w-full mx-4">
        <h2 class="text-2xl font-bold text-emerald-400 mb-4 text-center">
          Bem-vindo ao Campo Minado!
        </h2>
        <p class="text-gray-300 mb-6 text-center">
          Digite seu nome para começar a jogar:
        </p>
        <input
          v-model="inputPlayerName"
          @keyup.enter="setPlayerName"
          type="text"
          placeholder="Seu nome ou nick"
          class="w-full p-3 bg-gray-700 text-white rounded-lg border border-gray-600 focus:border-emerald-400 focus:outline-none mb-4"
          maxlength="20"
          autofocus
        />
        <button
          @click="setPlayerName"
          :disabled="!inputPlayerName.trim()"
          class="w-full bg-emerald-500 hover:bg-emerald-600 disabled:bg-gray-600 disabled:cursor-not-allowed text-white font-bold py-3 px-4 rounded-lg transition-all duration-300"
        >
          Começar a Jogar
        </button>
      </div>
    </div>

    <!-- Componente do Tabuleiro -->
    <MinesweeperBoard
      v-if="playerName"
      :player-name="playerName"
      @game-finished="handleGameFinished"
    />

    <!-- Ranking -->
    <div
      v-if="playerName && rankings.length > 0"
      class="mt-8 bg-gray-800 p-6 rounded-xl shadow-lg max-w-2xl w-full"
    >
      <h3 class="text-2xl font-bold text-emerald-400 mb-4 text-center">
        🏆 Ranking
      </h3>
      <div class="space-y-2">
        <div
          v-for="(rank, index) in rankings.slice(0, 10)"
          :key="index"
          class="flex justify-between items-center p-3 bg-gray-700 rounded-lg"
          :class="{ 'bg-emerald-800': rank.playerName === playerName }"
        >
          <div class="flex items-center space-x-3">
            <span class="text-lg font-bold text-emerald-400"
              >{{ index + 1 }}º</span
            >
            <span class="font-medium">{{ rank.playerName }}</span>
          </div>
          <div class="text-right">
            <div class="text-sm text-gray-300">
              {{ rank.boardSize }} células
            </div>
            <div class="font-bold">{{ formatTime(rank.time) }}</div>
          </div>
        </div>
      </div>
      <button
        @click="clearRanking"
        class="mt-4 w-full bg-red-600 hover:bg-red-700 text-white font-bold py-2 px-4 rounded-lg transition-all duration-300"
      >
        Limpar Ranking
      </button>
    </div>

    <!-- Instruções para jogar -->
    <div v-if="playerName" class="mt-8 text-center text-gray-400 max-w-xl">
      <p>Clique com o botão esquerdo para revelar uma célula.</p>
      <p>Clique com o botão direito para colocar uma bandeira.</p>
      <p class="mt-2 text-sm">
        O tabuleiro se expande automaticamente quando você se aproxima das
        bordas!
      </p>
    </div>
  </div>
</template>

<script>
import MinesweeperBoard from "./components/MinesweeperBoard.vue";

export default {
  name: "App",
  components: {
    MinesweeperBoard,
  },
  data() {
    return {
      playerName: "",
      inputPlayerName: "",
      rankings: [],
    };
  },
  mounted() {
    this.loadRankings();
  },
  methods: {
    setPlayerName() {
      if (this.inputPlayerName.trim()) {
        this.playerName = this.inputPlayerName.trim();
        localStorage.setItem("minesweeper_player_name", this.playerName);
      }
    },

    handleGameFinished(gameData) {
      if (gameData.won) {
        this.addToRanking(gameData);
      }
    },

    addToRanking(gameData) {
      const newRank = {
        playerName: this.playerName,
        time: gameData.time,
        boardSize: gameData.boardSize,
        date: new Date().toISOString(),
      };

      this.rankings.push(newRank);
      this.rankings.sort((a, b) => {
        // Primeiro por tamanho do tabuleiro (maior melhor), depois por tempo (menor melhor)
        if (a.boardSize !== b.boardSize) {
          return b.boardSize - a.boardSize;
        }
        return a.time - b.time;
      });

      this.saveRankings();
    },

    loadRankings() {
      const saved = localStorage.getItem("minesweeper_rankings");
      if (saved) {
        this.rankings = JSON.parse(saved);
      }

      const savedPlayerName = localStorage.getItem("minesweeper_player_name");
      if (savedPlayerName) {
        this.playerName = savedPlayerName;
      }
    },

    saveRankings() {
      localStorage.setItem(
        "minesweeper_rankings",
        JSON.stringify(this.rankings)
      );
    },

    clearRanking() {
      this.rankings = [];
      localStorage.removeItem("minesweeper_rankings");
    },

    formatTime(seconds) {
      const mins = Math.floor(seconds / 60);
      const secs = seconds % 60;
      return `${mins.toString().padStart(2, "0")}:${secs
        .toString()
        .padStart(2, "0")}`;
    },
  },
};
</script>