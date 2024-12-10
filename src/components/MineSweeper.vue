<template>
  <div class="minesweeper">
    <h2>扫雷游戏</h2>
    <p>点击方格清除雷区，右键标记地雷！</p>
    <div id="board" :style="boardStyle">
      <div
        v-for="(cell, index) in grid"
        :key="index"
        :class="['cell', { revealed: cell.revealed, flagged: cell.flagged }]"
        @click="revealCell(cell)"
        @contextmenu.prevent="toggleFlag(cell)"
      >
        <span v-if="cell.revealed">
          {{ cell.mine ? "💣" : cell.count > 0 ? cell.count : "" }}
        </span>
        <span v-else-if="cell.flagged">🚩</span>
      </div>
    </div>
    <button @click="resetGame">重新开始</button>
    <p v-if="message" class="message">{{ message }}</p>
  </div>
</template>

<script>
export default {
  data() {
    return {
      rows: 10,
      cols: 10,
      minesCount: 15,
      grid: [],
      revealedCount: 0,
      message: "",
    };
  },
  computed: {
    boardStyle() {
      return {
        gridTemplateRows: `repeat(${this.rows}, 1fr)`,
        gridTemplateColumns: `repeat(${this.cols}, 1fr)`,
      };
    },
  },
  methods: {
    initGame() {
      this.grid = [];
      this.revealedCount = 0;
      this.message = "";

      // 创建空网格
      for (let r = 0; r < this.rows; r++) {
        for (let c = 0; c < this.cols; c++) {
          this.grid.push({
            row: r,
            col: c,
            mine: false,
            revealed: false,
            flagged: false,
            count: 0,
          });
        }
      }

      // 随机布雷
      let minesPlaced = 0;
      while (minesPlaced < this.minesCount) {
        const randomIndex = Math.floor(Math.random() * this.grid.length);
        const cell = this.grid[randomIndex];
        if (!cell.mine) {
          cell.mine = true;
          minesPlaced++;
        }
      }

      // 计算每个格子的周围地雷数量
      this.grid.forEach((cell) => {
        if (!cell.mine) {
          cell.count = this.getMineCount(cell);
        }
      });
    },
    getMineCount(cell) {
      const directions = [
        [-1, -1],
        [-1, 0],
        [-1, 1],
        [0, -1],
        [0, 1],
        [1, -1],
        [1, 0],
        [1, 1],
      ];
      let count = 0;
      directions.forEach(([dr, dc]) => {
        const neighbor = this.getCell(cell.row + dr, cell.col + dc);
        if (neighbor && neighbor.mine) {
          count++;
        }
      });
      return count;
    },
    getCell(row, col) {
      return this.grid.find((cell) => cell.row === row && cell.col === col);
    },
    revealCell(cell) {
      if (cell.revealed || cell.flagged) return;

      cell.revealed = true;
      this.revealedCount++;

      if (cell.mine) {
        this.message = "游戏结束！你踩到地雷了！";
        this.revealAllMines();
        return;
      }

      if (cell.count === 0) {
        this.revealNeighbors(cell);
      }

      if (this.revealedCount === this.rows * this.cols - this.minesCount) {
        this.message = "恭喜！你赢了！";
      }
    },
    revealNeighbors(cell) {
      const directions = [
        [-1, -1],
        [-1, 0],
        [-1, 1],
        [0, -1],
        [0, 1],
        [1, -1],
        [1, 0],
        [1, 1],
      ];
      directions.forEach(([dr, dc]) => {
        const neighbor = this.getCell(cell.row + dr, cell.col + dc);
        if (neighbor && !neighbor.revealed && !neighbor.mine) {
          this.revealCell(neighbor);
        }
      });
    },
    toggleFlag(cell) {
      if (cell.revealed) return;
      cell.flagged = !cell.flagged;
    },
    revealAllMines() {
      this.grid.forEach((cell) => {
        if (cell.mine) {
          cell.revealed = true;
        }
      });
    },
    resetGame() {
      this.initGame();
    },
  },
  mounted() {
    this.initGame();
  },
};
</script>

<style scoped>
.minesweeper {
  text-align: center;
  margin: 20px auto;
  width: 330px;
}

#board {
  display: grid;
  gap: 2px;
  background: #ccc;
  padding: 5px;
  border-radius: 8px;
}

.cell {
  width: 30px;
  height: 30px;
  background: #ddd;
  text-align: center;
  line-height: 30px;
  font-size: 16px;
  font-weight: bold;
  cursor: pointer;
  border-radius: 4px;
}

.cell.revealed {
  background: #fff;
  cursor: default;
}

.cell.flagged {
  background: #f39c12;
  color: white;
}

.message {
  font-size: 16px;
  font-weight: bold;
  color: #2c3e50;
  margin-top: 10px;
}
</style>
