<script setup>
import { ref, onMounted, watch } from 'vue';
import BaseMine from './BaseMine.vue';
import { initGrid, flowFieldTo } from '@/utils/minesweeper.js';
import { correctlyMarkedMines, isWin } from '@/store/pad.js';

const rows = 10;
const cols = 10;
const mineCount = 15;
const gap = 0.1;
const width = 1;
const depth = 1;
const grid = ref([]);

// 🏆 Charger la grille au montage du composant
onMounted(() => {
  grid.value = initGrid(rows, cols, mineCount);
});

function revealed_propagation(x, y) {
  const flowMap = flowFieldTo(x, y, rows, cols, grid.value);
  for (let i = 0; i < rows; i++) {
    for (let j = 0; j < cols; j++) {
      if (flowMap[i][j] === false) continue;
      grid.value[i][j].showHint = true;
    }
  }
}

// 🎨 Déterminer dynamiquement si on gagne!
watch(correctlyMarkedMines, (newValue) => {
    if (newValue === mineCount) {
      console.log("🎉 Victoire ! Toutes les mines ont été trouvées !");
      isWin.value = true;
    }
  });

</script>

<template>
  <a-entity>
    
    <!-- Solution : Utiliser un <template> pour imbriquer les v-for -->
    <template v-for="(row, x) in grid" :key="x">
      <template v-for="(cell, y) in row" :key="`${x}-${y}`">
        <BaseMine
            @revealed-propagation="revealed_propagation(x, y)"
            :position="`${x * (width + gap)} 0 ${y * (depth + gap)}`"
            :isMine="cell.isMine"
            :showHint="cell.showHint"
            :revealed="cell.revealed"
            :adjacentMines="cell.adjacentMines"
            :depth="depth"
            :width="width"
        />
      </template>
    </template>
  </a-entity>
</template>
