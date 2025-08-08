<template>
  <div>
    <!-- Barra de Status -->
    <div 
      class="bg-gray-800 p-4 rounded-xl shadow-lg mb-6 w-full max-w-2xl flex justify-between items-center"
      :class="{ 'game-over-animation': gameOver }"
    >
      <span class="text-xl font-medium">Bandeiras: {{ flagsRemaining }}</span>
      <span class="text-xl font-medium">Tempo: {{ formatTime(gameTime) }}</span>
      <span 
        class="text-xl font-bold"
        :class="{
          'text-red-500 animate-pulse': gameOver,
          'text-green-500 win-animation': gameWon
        }"
      >
        {{ gameStatus }}
      </span>
      <button
        @click="resetGame"
        class="bg-emerald-500 hover:bg-emerald-600 text-white font-bold py-2 px-4 rounded-full transition-all duration-300 transform hover:scale-105 shadow-md"
      >
        Novo Jogo
      </button>
    </div>

    <!-- Tabuleiro do Campo Minado -->
    <div 
      class="bg-gray-800 p-2 rounded-xl shadow-xl overflow-auto"
      style="width: auto; max-width: 90vw; max-height: 70vh;"
    >
      <div class="flex flex-col gap-0.5">
        <div 
          v-for="(row, rowIndex) in board" 
          :key="rowIndex" 
          class="flex gap-0.5"
        >
          <div
            v-for="(cell, colIndex) in row"
            :key="colIndex"
            class="cell"
            :class="getCellClass(cell)"
            @click="handleCellClick(rowIndex, colIndex)"
            @contextmenu.prevent="handleRightClick(rowIndex, colIndex)"
          >
            {{ getCellContent(cell) }}
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'MinesweeperBoard',
  data() {
    return {
      board: [],
      rows: 10,
      cols: 10,
      gameOver: false,
      gameWon: false,
      mineCount: 0,
      flagsRemaining: 0,
      gameTime: 0,
      gameTimer: null,
      gameStarted: false,
      
      // Constantes
      MINE: 'X',
      HIDDEN: 'H',
      FLAG: 'F',
      REVEALED: 'R'
    }
  },
  computed: {
    gameStatus() {
      if (this.gameOver) return 'Game Over!'
      if (this.gameWon) return 'Você Venceu!'
      return ''
    }
  },
  mounted() {
    this.initializeBoard(this.rows, this.cols)
  },
  beforeUnmount() {
    if (this.gameTimer) {
      clearInterval(this.gameTimer)
    }
  },
  methods: {
    // Inicializa o tabuleiro do jogo
    initializeBoard(initialRows, initialCols) {
      this.board = Array(initialRows)
        .fill(null)
        .map(() =>
          Array(initialCols)
            .fill(null)
            .map(() => ({ value: 0, state: this.HIDDEN }))
        )

      const initialMineCount = Math.floor(initialRows * initialCols * 0.15)
      this.board = this.placeMinesAndNumbers(
        this.board,
        initialRows,
        initialCols,
        initialMineCount
      )

      this.mineCount = initialMineCount
      this.flagsRemaining = initialMineCount
      this.gameTime = 0
      this.gameStarted = false
      
      if (this.gameTimer) {
        clearInterval(this.gameTimer)
        this.gameTimer = null
      }
    },

    // Coloca minas e calcula números adjacentes
    placeMinesAndNumbers(currentBoard, currentRows, currentCols, numMines) {
      let newBoard = JSON.parse(JSON.stringify(currentBoard))
      let count = 0
      
      while (count < numMines) {
        const randomRow = Math.floor(Math.random() * currentRows)
        const randomCol = Math.floor(Math.random() * currentCols)

        if (newBoard[randomRow][randomCol].value !== this.MINE) {
          newBoard[randomRow][randomCol].value = this.MINE
          count++
        }
      }

      // Calcula números adjacentes
      for (let r = 0; r < currentRows; r++) {
        for (let c = 0; c < currentCols; c++) {
          if (newBoard[r][c].value === this.MINE) continue
          
          let mineCount = 0
          for (let i = -1; i <= 1; i++) {
            for (let j = -1; j <= 1; j++) {
              if (i === 0 && j === 0) continue
              const newR = r + i
              const newC = c + j
              if (
                newR >= 0 &&
                newR < currentRows &&
                newC >= 0 &&
                newC < currentCols &&
                newBoard[newR][newC].value === this.MINE
              ) {
                mineCount++
              }
            }
          }
          newBoard[r][c].value = mineCount
        }
      }
      
      return newBoard
    },

    // Expande o tabuleiro quando necessário
    expandBoard(row, col) {
      const expansionSize = 5
      let newRows = this.rows
      let newCols = this.cols
      let expanded = false

      // Expansão vertical
      if (row < 2) {
        newRows += expansionSize
        const emptyRows = Array(expansionSize)
          .fill(null)
          .map(() =>
            Array(this.cols)
              .fill(null)
              .map(() => ({ value: 0, state: this.HIDDEN }))
          )
        this.board = [...emptyRows, ...this.board]
        expanded = true
      } else if (row >= this.rows - 2) {
        newRows += expansionSize
        const emptyRows = Array(expansionSize)
          .fill(null)
          .map(() =>
            Array(this.cols)
              .fill(null)
              .map(() => ({ value: 0, state: this.HIDDEN }))
          )
        this.board = [...this.board, ...emptyRows]
        expanded = true
      }

      // Expansão horizontal
      if (col < 2) {
        newCols += expansionSize
        this.board = this.board.map((rowArr) => [
          ...Array(expansionSize)
            .fill(null)
            .map(() => ({ value: 0, state: this.HIDDEN })),
          ...rowArr,
        ])
        expanded = true
      } else if (col >= this.cols - 2) {
        newCols += expansionSize
        this.board = this.board.map((rowArr) => [
          ...rowArr,
          ...Array(expansionSize)
            .fill(null)
            .map(() => ({ value: 0, state: this.HIDDEN })),
        ])
        expanded = true
      }

      if (expanded) {
        const newMineCount = Math.floor(newRows * newCols * 0.15)
        const minesToPlace = newMineCount - this.mineCount

        this.board = this.placeMinesAndNumbers(this.board, newRows, newCols, minesToPlace)

        this.rows = newRows
        this.cols = newCols
        this.mineCount = newMineCount
        this.flagsRemaining = newMineCount
      }
    },

    // Revela uma célula e seus vizinhos se estiver vazia
    revealCell(r, c) {
      const currentRows = this.board.length
      const currentCols = this.board[0].length

      if (r < 0 || r >= currentRows || c < 0 || c >= currentCols) return
      
      const cell = this.board[r][c]
      if (cell.state !== this.HIDDEN) return

      cell.state = this.REVEALED

      if (cell.value === this.MINE) {
        this.gameOver = true
        return
      }

      if (cell.value === 0) {
        for (let i = -1; i <= 1; i++) {
          for (let j = -1; j <= 1; j++) {
            if (i === 0 && j === 0) continue
            this.revealCell(r + i, c + j)
          }
        }
      }
    },

    // Handler para clique esquerdo
    handleCellClick(row, col) {
      if (this.gameOver || this.gameWon) return

      // Inicia o timer no primeiro clique
      if (!this.gameStarted) {
        this.startTimer()
        this.gameStarted = true
      }

      this.expandBoard(row, col)

      const cell = this.board[row][col]
      if (cell.state === this.REVEALED || cell.state === this.FLAG) return

      if (cell.value === this.MINE) {
        this.gameOver = true
        this.stopTimer()
        this.revealAllMines()
        return
      }

      this.revealCell(row, col)
      this.checkWin()
    },

    // Handler para clique direito (bandeira)
    handleRightClick(row, col) {
      if (this.gameOver || this.gameWon) return

      const cell = this.board[row][col]

      if (cell.state === this.HIDDEN && this.flagsRemaining > 0) {
        cell.state = this.FLAG
        this.flagsRemaining--
      } else if (cell.state === this.FLAG) {
        cell.state = this.HIDDEN
        this.flagsRemaining++
      }
    },

    // Revela todas as minas quando o jogo termina
    revealAllMines() {
      const currentRows = this.board.length
      const currentCols = this.board[0].length

      for (let r = 0; r < currentRows; r++) {
        for (let c = 0; c < currentCols; c++) {
          if (this.board[r][c].value === this.MINE) {
            this.board[r][c].state = this.REVEALED
          }
        }
      }
    },

    // Verifica se o jogador venceu
    checkWin() {
      const currentRows = this.board.length
      const currentCols = this.board[0].length

      let revealedCount = 0
      for (let r = 0; r < currentRows; r++) {
        for (let c = 0; c < currentCols; c++) {
          if (this.board[r][c].state === this.REVEALED && this.board[r][c].value !== this.MINE) {
            revealedCount++
          }
        }
      }
      
      if (revealedCount === currentRows * currentCols - this.mineCount) {
        this.gameWon = true
        this.stopTimer()
      }
    },

    // Reseta o jogo
    resetGame() {
      this.gameOver = false
      this.gameWon = false
      this.rows = 10
      this.cols = 10
      this.stopTimer()
      this.initializeBoard(10, 10)
    },

    // Inicia o timer
    startTimer() {
      this.gameTimer = setInterval(() => {
        this.gameTime++
      }, 1000)
    },

    // Para o timer
    stopTimer() {
      if (this.gameTimer) {
        clearInterval(this.gameTimer)
        this.gameTimer = null
      }
    },

    // Formata o tempo em MM:SS
    formatTime(seconds) {
      const mins = Math.floor(seconds / 60)
      const secs = seconds % 60
      return `${mins.toString().padStart(2, '0')}:${secs.toString().padStart(2, '0')}`
    },

    // Retorna a classe CSS da célula
    getCellClass(cell) {
      let classes = []
      
      if (cell.state === this.HIDDEN) {
        classes.push('cell-hidden')
      } else if (cell.state === this.REVEALED) {
        if (cell.value === this.MINE) {
          classes.push('cell-mine')
        } else {
          classes.push('cell-revealed')
          if (cell.value > 0 && cell.value <= 8) {
            classes.push(`cell-number-${cell.value}`)
          }
        }
      } else if (cell.state === this.FLAG) {
        classes.push('cell-flagged')
      }
      
      return classes.join(' ')
    },

    // Retorna o conteúdo da célula
    getCellContent(cell) {
      if (cell.state === this.FLAG) {
        return '🚩'
      } else if (cell.state === this.REVEALED) {
        if (cell.value === this.MINE) {
          return '💣'
        } else if (cell.value !== 0) {
          return cell.value
        }
      }
      return ''
    }
  }
}
</script>

