<script setup lang="ts">
import Cell from "./components/Cell.vue";
import {ref} from "vue";

const scoring = ref(false);
const score = ref(0);
const fallenScore = ref(0);
const cells = ref([]);

const numberSelected = ref(0);

function updateScore(pts: number) {
    score.value += pts;
    fallenScore.value += Math.abs(pts) >= 2 ? pts - Math.sign(pts) : pts;
}
function updateSelected(selected: boolean) {
    numberSelected.value += selected ? 1 : -1;
}
function reset() {
    scoring.value = false;
    score.value = 0;
    fallenScore.value = 0;
    numberSelected.value = 0;
    cells.value.forEach(cell => cell.reset());
}
</script>

<template>
  <div class="text-white text-center">
      <template v-if="!scoring"> Tap on squares to select them. </template>
      <template v-else>
          Tap on a square once for two points, or twice for three points.<br>
          Record your score before pressing the clear button. (The red score is your score if you fell in the dark.)
      </template>
  </div>
  <div class="h-[80%] w-[80%] flex">
      <div class="bg-gradient-to-tl from-violet-700 via-purple-500 to-purple-300 h-full w-24 rounded-l-lg"></div>
      <div class="rounded-r-lg h-full bg-slate-900 w-full grid grid-cols-5 p-3 gap-4">
          <Cell v-for="_ in new Array(15)" :scoring="scoring" @score="updateScore" @select="updateSelected" ref="cells" :num-selected="numberSelected"/>
      </div>
      <div class="flex flex-col items-center justify-center ml-4 gap-y-2">
          <div class="rounded-full aspect-square bg-white flex items-center justify-center text-3xl font-bold w-full">{{scoring ? score : numberSelected}}</div>
          <button @click="scoring = true" v-if="!scoring" class="bg-green-400"> → </button>
          <button @click="reset" v-else class="bg-red-500"> → </button>
          <div class="rounded-full aspect-square bg-red-300 flex items-center justify-center text-3xl font-bold w-full" v-if="scoring">{{fallenScore}}</div>
      </div>
  </div>
</template>

<style scoped>
button {
    @apply rounded-lg p-2 text-white text-3xl;
}
</style>
