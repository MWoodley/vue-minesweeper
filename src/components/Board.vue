<template>
  <div class="">
    <div class="relative flex justify-center">
      <div class=" overflow-x-auto">
        <div
          v-for="(row, i) in cells"
          :key="'row' + i"
          class="flex flex-no-wrap w-full"
        >
          <cell
            v-for="(col, j) in row"
            :key="'col' + j"
            :cellData="col"
            :cellSize="cellSize"
            @cellClicked="onCellClicked"
            @cellFlagged="onCellFlagged"
          ></cell>
        </div>
        <div
          v-if="loading"
          class="overlay absolute flex justify-center py-12 inset-0 font-bold text-black leading-none"
        >
          LOADING
        </div>
        <div
          v-if="paused"
          class="overlay absolute inset-0 flex items-center justify-center font-bold text-black"
        >
          <div>
            <div class="text-5xl mb-2">You {{ won ? "Won" : "Lost" }}!</div>
            <div class="text-center">
              <button
                class="px-12 py-4 bg-blue-500 hover:bg-blue-600 uppercase font-bold text-white"
                v-on:click="initNewGame"
              >
                Play again
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
    <div v-if="!loading" class="pt-4 pb-8">
      <div class="mb-4">
        <div>
          <span class="font-bold">Cells Remaining:</span> {{ cellsRemaining }}
        </div>
        <div>
          <span class="font-bold">Flags Remaining: </span> {{ flagsRemaining }}
        </div>
      </div>
      <form action="">
        <div class="-mx-4 flex flex-wrap">
          <div class="px-4 w-full md:w-1/3 pb-4">
            <label
              for="inputRows"
              class="block font-bold text-black text-sm mb-2"
              >Num rows</label
            >
            <input
              type="number"
              name="inputRows"
              v-model="inputs.rows"
              class="border py-2 px-2 w-full"
            />
          </div>
          <div class="px-4 w-full md:w-1/3 pb-4">
            <label
              for="inputCols"
              class="block font-bold text-black text-sm mb-2"
              >Num cols</label
            >
            <input
              type="number"
              name="inputCols"
              v-model="inputs.cols"
              class="border py-2 px-2 w-full"
            />
          </div>
          <div class="px-4 w-full md:w-1/3 pb-4">
            <label
              for="inputMines"
              class="block font-bold text-black text-sm mb-2"
              >Num mines</label
            >
            <input
              type="number"
              name="inputMines"
              v-model="inputs.mines"
              class="border py-2 px-2 w-full"
            />
          </div>
        </div>
        <div>
          <button
            class="px-6 py-3 bg-blue-500 hover:bg-blue-600 uppercase font-bold text-white"
            v-on:click="initNewGame"
          >
            Create new game
          </button>
        </div>
      </form>
    </div>
  </div>
</template>

<script>
  import Cell from "./Cell.vue";

  export default {
    components: {
      Cell
    },
    props: ["cellSize"],
    data: function() {
      return {
        rows: 15,
        cols: 15,
        mines: 75,
        loading: true,
        cells: [],
        won: false,
        lost: false,
        inputs: {
          rows: 15,
          cols: 15,
          mines: 75
        }
      };
    },
    beforeMount() {
      this.initGame();
    },
    computed: {
      paused: function() {
        return this.won || this.lost;
      },
      cellsRemaining: function() {
        if (this.cells.length === 0) {
          return 0;
        }
        return this.cells.reduce(
          (count, row) =>
            count + row.filter(cell => !cell.clicked && !cell.isMine).length,
          0
        );
      },
      flagsPlaced: function() {
        if (this.cells.length === 0) {
          return 0;
        }
        return this.cells.reduce(
          (count, row) => count + row.filter(cell => cell.flagged).length,
          0
        );
      },
      minesFlagged: function() {
        if (this.cells.length === 0) {
          return 0;
        }
        return this.cells.reduce(
          (count, row) =>
            count + row.filter(cell => cell.flagged && cell.isMine).length,
          0
        );
      },
      minesRemaining: function() {
        return this.mines - this.minesFlagged;
      },
      flagsRemaining: function() {
        return this.mines - this.flagsPlaced;
      }
    },
    methods: {
      initNewGame(e) {
        e.preventDefault();
        this.setupInitVars();
        this.initGame();
        this.$emit("showAlert");
      },
      setupInitVars() {
        let rows = this.inputs.rows <= 0 ? 24 : this.inputs.rows;
        let cols = this.inputs.cols <= 0 ? 30 : this.inputs.cols;
        let totalMines = rows * cols;
        let mines =
          this.inputs.mines > totalMines ? totalMines : this.inputs.mines;
        mines = mines < 0 ? 200 : mines;

        this.rows = rows;
        this.cols = cols;
        this.mines = mines;
        this.inputs.rows = rows;
        this.inputs.cols = cols;
        this.inputs.mines = mines;
      },
      initGame() {
        this.loading = true;
        this.won = false;
        this.lost = false;
        this.cells = [];

        let cells = [];
        for (let i = 0; i < this.rows; i++) {
          let row = [];
          for (let j = 0; j < this.cols; j++) {
            row.push({
              row: i,
              col: j,
              isMine: false,
              clicked: false,
              flagged: false
            });
          }
          cells.push(row);
        }

        let minesPlaced = 0;
        while (minesPlaced < this.mines) {
          let row = Math.floor(Math.random() * this.rows);
          let col = Math.floor(Math.random() * this.cols);
          if (!cells[row][col].isMine) {
            cells[row][col].isMine = true;
            minesPlaced += 1;
          }
        }

        cells.map(row => {
          return row.map(cell => {
            if (cell.isMine) {
              return null;
            }
            let adjacentMines = 0;
            for (let i = -1; i <= 1; i++) {
              if (cell.row + i < 0 || cell.row + i >= this.rows) {
                continue;
              }
              for (let j = -1; j <= 1; j++) {
                if (cell.col + j < 0 || cell.col + j >= this.cols) {
                  continue;
                }
                if (cells[cell.row + i][cell.col + j].isMine) {
                  adjacentMines += 1;
                }
              }
            }
            cell.adjacentMines = adjacentMines;
            return cell;
          });
        });

        this.cells = cells;
        this.loading = false;
      },
      onCellClicked(data) {
        if (this.paused) {
          return;
        }
        let cell = this.cells[data.row][data.col];
        if (cell.clicked) {
          return;
        }

        if (cell.flagged) {
          return;
        }

        cell.clicked = true;

        if (cell.isMine) {
          this.lost = true;
          return;
        }

        for (let i = -1; i <= 1; i++) {
          if (cell.row + i < 0 || cell.row + i >= this.rows) {
            continue;
          }
          if (!this.cells[cell.row + i][cell.col].isMine) {
            this.onCellClicked(this.cells[cell.row + i][cell.col]);
          }
        }
        for (let i = -1; i <= 1; i++) {
          if (cell.col + i < 0 || cell.col + i >= this.cols) {
            continue;
          }
          if (!this.cells[cell.row][cell.col + i].isMine) {
            this.onCellClicked(this.cells[cell.row][cell.col + i]);
          }
        }

        if (this.cellsRemaining === 0) {
          this.won = true;
        }
      },
      onCellFlagged(data) {
        if (this.paused) {
          return;
        }
        let cell = this.cells[data.row][data.col];
        if (cell.clicked) {
          return;
        }
        if (this.flagsRemaining === 0 && !cell.flagged) {
          return;
        }
        cell.flagged = !cell.flagged;

        if (this.minesRemaining === 0) {
          this.won = true;
        }
      }
    }
  };
</script>

<style>
  .overlay {
    background: rgba(0, 0, 0, 0.5);
    color: white;
  }
</style>
