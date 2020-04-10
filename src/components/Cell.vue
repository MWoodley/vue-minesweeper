<template>
  <div
    class="p-px"
    :style="`min-width: ${cellSize}px; min-height: ${cellSize}px;`"
  >
    <button
      class="w-full h-full flex items-center justify-center"
      :class="
        cellData.clicked && cellData.isMine
          ? 'bg-red-500 text-white cursor-auto'
          : cellData.clicked
          ? 'bg-blue-100 cursor-auto'
          : 'bg-blue-500 hover:bg-blue-600 text-white'
      "
      v-on:click="onClicked"
      v-on:contextmenu="rightClicked"
    >
      <div v-if="!cellData.flagged && !cellData.isMine" class="leading-none">
        {{ displayValue }}
      </div>
      <div v-if="cellData.flagged" class="leading-none">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 20 20"
          class="fill-current text-white h-4 w-4"
        >
          <path d="M7.667 12H2v8H0V0h12l.333 2H20l-3 6 3 6H8l-.333-2z" />
        </svg>
      </div>
      <div v-if="cellData.isMine && cellData.clicked" class="leading-none">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 50 50"
          class="fill-current text-white h-4 w-4"
        >
          <path
            d="M 24.984375 1.9863281 A 1.0001 1.0001 0 0 0 24 3 L 24 7.0488281 C 19.788099 7.2817221 15.969452 8.9603227 13.021484 11.607422 L 10.150391 8.7363281 A 1.0001 1.0001 0 0 0 9.4335938 8.4335938 A 1.0001 1.0001 0 0 0 8.7363281 10.150391 L 11.603516 13.017578 C 8.9676792 15.970574 7.2834883 19.792069 7.0507812 24 L 3 24 A 1.0001 1.0001 0 1 0 3 26 L 7.0507812 26 C 7.2834883 30.207931 8.9676792 34.029426 11.603516 36.982422 L 8.7363281 39.849609 A 1.0001 1.0001 0 1 0 10.150391 41.263672 L 13.021484 38.392578 C 15.969452 41.039677 19.788099 42.718278 24 42.951172 L 24 47 A 1.0001 1.0001 0 1 0 26 47 L 26 42.951172 C 30.211901 42.718278 34.030548 41.039677 36.978516 38.392578 L 39.849609 41.263672 A 1.0001 1.0001 0 1 0 41.263672 39.849609 L 38.396484 36.982422 C 41.032321 34.029426 42.716512 30.207931 42.949219 26 L 47 26 A 1.0001 1.0001 0 1 0 47 24 L 42.949219 24 C 42.716512 19.792069 41.032321 15.970574 38.396484 13.017578 L 41.263672 10.150391 A 1.0001 1.0001 0 0 0 40.537109 8.4335938 A 1.0001 1.0001 0 0 0 39.849609 8.7363281 L 36.978516 11.607422 C 34.030548 8.9603227 30.211901 7.2817221 26 7.0488281 L 26 3 A 1.0001 1.0001 0 0 0 24.984375 1.9863281 z M 20.5 15 C 23.538 15 26 17.462 26 20.5 C 26 23.538 23.538 26 20.5 26 C 17.462 26 15 23.538 15 20.5 C 15 17.462 17.462 15 20.5 15 z"
          />
        </svg>
      </div>
    </button>
  </div>
</template>

<script>
  export default {
    props: ["cellData", "cellSize"],
    methods: {
      onClicked() {
        if (!this.cellData.clicked) {
          this.$emit("cellClicked", this.cellData);
        }
      },
      rightClicked(e) {
        e.preventDefault();
        this.$emit("cellFlagged", this.cellData);
      }
    },
    computed: {
      displayValue() {
        if (this.cellData.clicked) {
          if (this.cellData.isMine) {
            return "M";
          }
          return this.cellData.adjacentMines || "";
        }
        if (this.cellData.flagged) {
          return "";
        }
        return "";
      }
    }
  };
</script>

<style></style>
