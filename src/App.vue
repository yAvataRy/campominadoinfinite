<template>
  <div class="bg-gray-900 text-white min-h-screen flex flex-col items-center justify-center font-sans p-4">
    <!-- Título do Jogo -->
    <h1 class="text-4xl font-bold mb-4 text-emerald-400">Campo Minado</h1>
    
    <!-- Componente do Tabuleiro -->
    <MinesweeperBoard />
    
    <!-- Instruções para jogar -->
    <div class="mt-8 text-center text-gray-400 max-w-xl">
      <p>Clique com o botão esquerdo para revelar uma célula.</p>
      <p>Clique com o botão direito para colocar uma bandeira.</p>
      <p class="mt-2 text-sm">O tabuleiro se expande automaticamente quando você se aproxima das bordas!</p>
    </div>
  </div>
</template>

<script>
import MinesweeperBoard from './components/MinesweeperBoard.vue'

export default {
  name: 'App',
  components: {
    MinesweeperBoard
  }
}
</script>

