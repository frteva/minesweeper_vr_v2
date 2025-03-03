<script setup>
import { ref, computed, watch } from 'vue';
import '../aframe/clickable.js';
import '../aframe/event-set.js';
import '../aframe/look-at.js';
import '../aframe/outline.js';
import { markdownPressed, correctlyMarkedMines } from '../store/pad.js';

const props = defineProps({
  position: String,
  isMine: Boolean,
  showHint: Boolean,
  adjacentMines: Number,
  width: { type: Number, default: 1 },
  depth: { type: Number, default: 1 },
});

const emits = defineEmits(['revealed-propagation']);
const clicked = ref(false);
const markedAsMine = ref(false); // 🚩 Marquer une case comme suspectée
const correctlyMarkedMine = ref(false);
let unwatch = null;

watch(correctlyMarkedMine, (newValue) => {
  console.log(`🔍 correctlyMarkedMine changé : ${newValue}`);
  if (newValue) {
    correctlyMarkedMines.value++;
  } else {
    correctlyMarkedMines.value--;
  }
});

// 🎨 Déterminer dynamiquement la couleur
const boxColor = computed(() => {
  if (markedAsMine.value) return 'purple'; // 🟣 Case marquée comme mine
  if (props.isMine && clicked.value) return 'red'; // 🔴 Devient rouge après un clic
  if (props.showHint && props.adjacentMines > 0) return 'green'; // ✅ Devient vert
  return 'blue'; // 🔵 Sinon, reste bleu
});

// 🎵 Déterminer le bon son en fonction des mines adjacentes
const hoverSoundId = computed(() => {
  return boxColor.value === 'blue' ? 'sound-hidden' : `sound-${props.adjacentMines}`;
});

function handleMouseEnter() {
  // 🎯 Active le `watch` pour détecter la pression sur `abuttondown`
  unwatch = watch(markdownPressed, (newValue) => {
    if (!markdownPressed.value) return;
    handleMarkMine();
  });

  console.log("🔊 BaseMine.vue: Jouer son :", hoverSoundId.value);

  const soundContainer = document.querySelector("#sound-container");
  if (!soundContainer) {
    console.error("❌ BaseMine.vue: sound-container non trouvé !");
    return;
  }

  // Stopper tous les sons et jouer le bon
  soundContainer.querySelectorAll("a-entity[sound]").forEach(entity => {
    if (entity.components.sound) entity.components.sound.stopSound();
  });

  const soundEntity = document.querySelector(`#sound-container #${hoverSoundId.value}`);
  if (soundEntity && soundEntity.components.sound) {
    soundEntity.components.sound.playSound();
    console.log("▶️ Son joué :", hoverSoundId.value);
  } else {
    console.error("❌ BaseMine.vue: Son non trouvé :", hoverSoundId.value);
  }
}

// 🎵 Arrêter tous les sons au `mouseleave`
function handleMouseLeave() {
  if (unwatch) {
    unwatch();
  }

  console.log("⏹ BaseMine.vue: Stopper tous les sons");

  const soundContainer = document.querySelector("#sound-container");
  if (!soundContainer) return;

  soundContainer.querySelectorAll("a-entity[sound]").forEach(entity => {
    if (entity.components.sound) entity.components.sound.stopSound();
  });
}

// 🚩 Marquer une case comme suspectée
function handleMarkMine() {
  if (clicked.value) return;
  markedAsMine.value = !markedAsMine.value;
  console.log(`🚩 Case ${props.position} marquée comme mine : ${markedAsMine.value}`);
  if(markedAsMine.value && props.isMine) {
    correctlyMarkedMine.value = true;
  } else {
    correctlyMarkedMine.value = false;
  }
}

// 🎯 Révéler la case (si elle était marquée violette, appliquer la révélation)
function handleClick() {
  if (markedAsMine.value) {
    markedAsMine.value = false; // Retirer le marquage

    if (props.isMine) {
      clicked.value = true; // 🚨 Devient rouge car c'est une mine
    } else if (props.showHint && props.adjacentMines > 0) {
      // ✅ Devient vert s'il y a des mines adjacentes
    } else {
      // 🔵 Devient invisible si `adjacentMines === 0`
      emits("revealed-propagation");
    }
  } else {
    if (!props.isMine) {
      emits("revealed-propagation");
    } else {
      console.log("💥 BOOM! C'était une mine !");
      clicked.value = true;
    }
  }
}
</script>

<template>
  <a-box
    clickable
    @click="handleClick"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
    outline-on-event
    :position="position"
    :material="`color: ${boxColor}; opacity: ${(showHint && adjacentMines == 0 && !isMine) ? 0 : 1}`"
    :depth="depth"
    :width="width"
    height="0.2"
  >
    <!-- Afficher le nombre si révélé et non mine -->
    <a-text v-if="showHint && !isMine"
      :value="adjacentMines"
      position="0 0.5 0"
      look-at
    >
    </a-text>
  </a-box>
</template>
