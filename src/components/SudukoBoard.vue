<template>
  <div>
    <h1>Sudoku Solver</h1>
    <div class="board">
      <div
        v-for="(row, rowIndex) in puzzle"
        :key="rowIndex"
        class="row"
        :class="(rowIndex + 1) % 3 === 0 ? 'thickBorder' : ''"
      >
        <div
          v-for="(cell, cellIndex) in row"
          :key="cellIndex"
          class="cell"
          :class="[
            (cellIndex + 1) % 3 === 0 ? 'thickBorder' : null,
            initialNumbers.includes(`${rowIndex},${cellIndex}`) ? 'bold' : null,
          ]"
        >
          <input
            type="text"
            :value="cell"
            @input="puzzle[rowIndex][cellIndex] = $event.target.value === '' ? null : parseInt($event.target.value)"
          />
        </div>
      </div>
    </div>
    <div class="controls">
      <button @click="solve">Solve</button>
      <button @click="clearBoard">Clear Board</button>
    </div>
  </div>
</template>

<script>
export default {
  data() {
    return {
      puzzle: [
        [null, null, null, 6, null, 2, null, null, null],
        [null, 8, null, 4, null, 5, null, 1, null],
        [5, 1, null, null, null, null, null, 6, 3],
        [null, null, 9, 2, null, 3, 5, null, null],
        [null, 4, null, null, null, null, null, 7, null],
        [null, null, 5, 8, null, 4, 9, null, null],
        [2, 3, null, null, null, null, null, 9, 4],
        [null, 5, null, 3, null, 1, null, 8, null],
        [null, null, null, 9, null, 6, null, null, null],
      ],
      possibilityMatrix: [],
      initialNumbers: [],
    };
  },
  methods: {
    clearBoard() {
      this.puzzle.forEach((row, rowIndex) => {
        row.forEach((cell, cellIndex) => {
          this.puzzle[rowIndex][cellIndex] = null;
        });
      });
      this.$forceUpdate();
    },
    getColumn(cellIndex) {
      const col = [];
      this.puzzle.forEach((row) => {
        col.push(row[cellIndex]);
      });
      return col;
    },
    getBox(rowIndex, cellIndex) {
      let startColIndex = 0;
      let endColIndex = 0;
      let startRowIndex = 0;
      let endRowIndex = 0;
      if (cellIndex >= 0 && cellIndex < 3) {
        startColIndex = 0;
      } else if (cellIndex >= 3 && cellIndex < 6) {
        startColIndex = 3;
      } else {
        startColIndex = 6;
      }
      if (rowIndex >= 0 && rowIndex < 3) {
        startRowIndex = 0;
      } else if (rowIndex >= 3 && rowIndex < 6) {
        startRowIndex = 3;
      } else {
        startRowIndex = 6;
      }
      endColIndex = startColIndex + 2;
      endRowIndex = startRowIndex + 2;

      const puzzleRows = this.puzzle.slice(startRowIndex, endRowIndex + 1);
      const box = [];
      puzzleRows.forEach((row) => {
        box.push(...row.slice(startColIndex, endColIndex + 1));
      });
      return box;
    },
    solve() {
      this.possibilityMatrix = [];
      this.puzzle.forEach((row, rowIndex) => {
        this.possibilityMatrix[rowIndex] = [];
        row.forEach((cell, cellIndex) => {
          // Check if null first
          if (!cell) {
            const col = this.getColumn(cellIndex);
            const box = this.getBox(rowIndex, cellIndex);
            this.possibilityMatrix[rowIndex][cellIndex] = [];
            for (let i = 1; i <= 9; i += 1) {
              if (!row.includes(i) && !col.includes(i) && !box.includes(i)) {
                this.possibilityMatrix[rowIndex][cellIndex].push(i);
              }
            }
          }
        });
      });
      let incomplete = false;
      this.possibilityMatrix.forEach((row, rowIndex) => {
        // console.log(row);
        row.forEach((cell, cellIndex) => {
          // console.log(rowIndex, cellIndex);
          if (cell && cell.length === 1) {
            incomplete = true;
            // eslint-disable-next-line prefer-destructuring
            this.puzzle[rowIndex][cellIndex] = cell[0];
          }
        });
      });
      setTimeout(() => {
        if (incomplete) {
          this.solve();
        }
        this.$forceUpdate();
      }, 250);
    },
  },
  mounted() {
    this.puzzle.forEach((row, rowIndex) => {
      row.forEach((cell, cellIndex) => {
        if (cell) {
          this.initialNumbers.push(`${rowIndex},${cellIndex}`);
        }
      });
    });
  },
};
</script>

<style lang="scss" scoped>
.board {
  display: inline-block;
  border-top: 3px solid black;
  border-left: 3px solid black;
}
.row {
  display: flex;
  border-bottom: 1px solid black;
  &.thickBorder {
    border-width: 3px;
  }
}
.cell {
  display: flex;
  justify-content: center;
  align-items: center;
  width: 20px;
  height: 20px;
  padding: 5px;
  border-right: 1px solid black;

  &.thickBorder {
    border-width: 3px;
  }
  &.bold {
    input {
      font-weight: bold;
    }
  }

  input {
    width: 100%;
    border: 0;
    text-align: center;
  }
}
.controls {
  margin-top: 20px;
  text-align: center;
}
</style>
