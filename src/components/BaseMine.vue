<script setup>
import { ref, computed, watch, onMounted } from 'vue';
import '../aframe/clickable.js';
import '../aframe/event-set.js';
import '../aframe/look-at.js';
import '../aframe/outline.js';
import { markdownPressed, correctlyMarkedMines, isDead } from '../store/pad.js';

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
const planeRef = ref(null);
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
const lightTexture = computed(() => {
  if (markedAsMine.value) return "blue"; // 🟣 Case marquée comme mine
  if (props.isMine && clicked.value) return "red"; // 🔴 Devient rouge après un clic
  if (props.showHint && props.adjacentMines > 0) return "green"; // ✅ Devient vert
  if(props.showHint && props.adjacentMines === 0) return "transparent"; // 🔵 Devient invisible
  return "purple"; // 🔵 Sinon, reste bleu
});

// Charger la texture au montage
onMounted(() => {
  if (planeRef.value) {
    planeRef.value.setAttribute("material", `src: #light_${lightTexture.value}; transparent: true; alphaTest: 0.01; opacity: 0.7; side: double`);
  }
});

// Mettre à jour la texture dynamiquement
watch(lightTexture, (newValue) => {
  if (planeRef.value) {
    planeRef.value.setAttribute("material", `src: #light_${newValue}; transparent: true; alphaTest: 0.01; opacity: 0.7; side: double`);
  }
});


// 🎵 Déterminer le bon son en fonction des mines adjacentes
const hoverSoundId = computed(() => {
  return lightTexture.value === 'purple' ? 'sound-hidden' : `sound-${props.adjacentMines}`;
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
      isDead.value = true; // 🟥 Met la variable `isDead` à `true`
    }
  }
}
</script>

<template>

  <a-plane 
    clickable
    @click="handleClick"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
    outline-on-event
    :position="position"
    rotation="-90 0 0" 
    :depth="depth"
    :width="width"
    ref="planeRef"
  >

  <a-entity v-if="isMine && clicked"
        gltf-model="#explosion"
        position="0 0 0" 
        scale="0.5 0.5 0.5"
        rotation="90 0 0"
        animation-mixer="clip: *; loop: once; timeScale: 4; clampWhenFinished: true;"
  ></a-entity>

   <!--  <a-text v-if="showHint && !isMine"
        :value="adjacentMines"
        position="0 0 0.2"
        look-at
      >
    </a-text> -->

  </a-plane>

</template>
