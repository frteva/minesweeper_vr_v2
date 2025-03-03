<script setup>
import { ref, computed, onMounted } from 'vue';
import '../aframe/clickable.js';
import '../aframe/event-set.js';
import '../aframe/look-at.js';
import '../aframe/outline.js';

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

// 🎨 Déterminer dynamiquement la couleur
const boxColor = computed(() => {
  if (markedAsMine.value) return 'purple'; // 🟣 Case marquée comme mine
  if (props.isMine && clicked.value) return 'red'; // 🔴 Devient rouge après un clic
  if (props.showHint) return 'green'; // ✅ Devient vert si `showHint === true`
  return 'blue'; // 🔵 Sinon, reste bleu
});

// 🎵 Déterminer le bon son en fonction des mines adjacentes
const hoverSoundId = computed(() => {
  return boxColor.value === 'blue' ? 'sound-hidden' : `sound-${props.adjacentMines}`;
});

// 🎵 Stopper tous les sons et jouer le bon
function handleMouseEnter() {
  console.log("🔊 BaseMine.vue: Jouer son :", hoverSoundId.value);

  // Récupérer le conteneur des sons
  const soundContainer = document.querySelector("#sound-container");
  if (!soundContainer) {
    console.error("❌ BaseMine.vue: sound-container non trouvé !");
    return;
  }

  // Stopper tous les sons
  soundContainer.querySelectorAll("a-entity[sound]").forEach(entity => {
    if (entity.components.sound) entity.components.sound.stopSound();
  });

  // Jouer uniquement le bon son
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
  console.log("⏹ BaseMine.vue: Stopper tous les sons");

  const soundContainer = document.querySelector("#sound-container");
  if (!soundContainer) return;

  soundContainer.querySelectorAll("a-entity[sound]").forEach(entity => {
    if (entity.components.sound) entity.components.sound.stopSound();
  });
}


function handleClick() {
  if (!props.isMine) {
    emits("revealed-propagation");
  } else {
    console.log("💥 BOOM! C'était une mine !");
    clicked.value = true;
  }
}

// 🚩 Fonction pour marquer une case comme suspectée
function handleMarkMine() {
  if (!clicked.value) {
    markedAsMine.value = !markedAsMine.value; // Alternance bleu/violet
    console.log(`🚩 Case ${props.position} marquée comme mine : ${markedAsMine.value}`);
  }
}

// 🕹 Ajouter l'écouteur de `abuttondown`
onMounted(() => {
  const entity = document.querySelector(`[position='${props.position}']`);
  if (!entity) return;

  entity.addEventListener("abuttondown", handleMarkMine); // VR
  document.addEventListener("keydown", (e) => {
    if (e.key === "m") handleMarkMine(); // Alternative PC : touche "M"
  });
});


</script>

<template>
  <a-box
    clickable
    @click="handleClick"
    @mouseenter="handleMouseEnter"
    @mouseleave="handleMouseLeave"
    outline-on-event
    :position="position"
    :material="`color: ${boxColor}; opacity: ${showHint && adjacentMines == 0 && !isMine ? 0 : 1}`"
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
