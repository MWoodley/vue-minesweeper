<template>
  <div id="app">
    <div
      class="fixed top-0 left-0 ml-8 mt-8 px-4 py-4 rounded-sm border shadow bg-white leading-none flex justify-between items-center text-gray-900 transition-all duration-250 z-50"
      v-if="alertShown"
    >
      <div class="mr-32">
        New game started
      </div>
      <button class="p-2" v-on:click="alertShown = false">
        <svg
          xmlns="http://www.w3.org/2000/svg"
          viewBox="0 0 20 20"
          class="h-4 w-4 fill-current"
        >
          <path
            d="M10 8.586L2.929 1.515 1.515 2.929 8.586 10l-7.071 7.071 1.414 1.414L10 11.414l7.071 7.071 1.414-1.414L11.414 10l7.071-7.071-1.414-1.414L10 8.586z"
          />
        </svg>
      </button>
    </div>
    <div class="mx-auto max-w-6xl w-full px-4">
      <div
        class="flex justify-between items-start md:items-end flex-col md:flex-row mt-6 mb-4"
      >
        <h1 class="text-4xl font-semibold text-gray-900 mb-2 md:mb-0">
          Minesweeper
        </h1>
        <div class="flex items-center">
          <label
            for="inputRows"
            class="block font-bold text-black text-sm mr-4 leading-none"
            >Cell size:
          </label>
          <select
            name="cellSize"
            id=""
            class="px-4 border text-sm"
            v-model="cellSize"
          >
            <option value="28">Extra Small</option>
            <option value="32">Small</option>
            <option value="36">Medium</option>
            <option value="40">Large</option>
            <option value="44">Extra Large</option>
          </select>
        </div>
      </div>
      <board :cellSize="cellSize" @showAlert="showAlert"></board>
    </div>
  </div>
</template>

<script>
  import Board from "./components/Board.vue";

  export default {
    name: "App",
    components: {
      Board
    },
    data: function() {
      return {
        alertShown: false,
        cellSize: 36
      };
    },
    methods: {
      showAlert() {
        this.alertShown = true;
        setTimeout(() => {
          this.alertShown = false;
        }, 2500);
      }
    }
  };
</script>

<style></style>
