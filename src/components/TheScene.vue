<script setup>
  import { ref, onMounted } from 'vue';

  import TheCameraRig from './TheCameraRig.vue';
  import MinesweeperGrid from './MinesweeperGrid.vue';
  import '../aframe/clickable.js';
  import '../aframe/pause-sound.js';
  import '../aframe/toggle-event.js';

  import '../aframe/simple-grab.js';
  import '../aframe/outline.js';

  defineProps({
    scale: Number,
    overlaySelector: String,
  });

  const allAssetsLoaded = ref(false);

  const onAssetsLoaded = () => {
  console.log("✅ Tous les assets sont chargés !");
  
  // Forcer le chargement des sons en mémoire
  for (let i = 0; i <= 8; i++) {
    const audioElement = document.querySelector(`#hover-sound-${i}`);
    if (audioElement) {
      audioElement.load();
      console.log(`🔊 Chargement forcé : hover-sound-${i}`);
    }
  }
};

</script>

<template>
  <a-scene
    stats
    fog="type: exponential; color: #000033; density: 0"
    background="color: #a3d0ed;"
    :webxr="`
      requiredFeatures: local-floor;
      referenceSpaceType: local-floor;
      optionalFeatures: dom-overlay;
      overlayElement: ${overlaySelector};
    `"
    xr-mode-ui="XRMode: xr"
    physx="
      autoLoad: true;
      delay: 1000;
      useDefaultScene: false;
      wasmUrl: lib/physx.release.wasm;
    "
    outline
    simple-grab
  >

  <a-assets @loaded="allAssetsLoaded = true">
    <!-- Préchargement du son -->
    <audio id="crazy-frog" src="./assets/audio/Crazy-Frog-Axel-F.mp3" preload="auto"></audio>
    <audio id="hover-sound-hidden" src="./assets/audio/hover_sound_hidden.mp3" preload="auto"></audio>
    <audio id="hover-sound-1" src="./assets/audio/hover_sound_1.mp3" preload="auto"></audio>
    <audio id="hover-sound-2" src="./assets/audio/hover_sound_2.mp3" preload="auto"></audio>
    <audio id="hover-sound-3" src="./assets/audio/hover_sound_3.mp3" preload="auto"></audio>
    <audio id="hover-sound-4" src="./assets/audio/hover_sound_4.mp3" preload="auto"></audio>
    <audio id="hover-sound-5" src="./assets/audio/hover_sound_5.mp3" preload="auto"></audio>
    <audio id="hover-sound-6" src="./assets/audio/hover_sound_6.mp3" preload="auto"></audio>
    <audio id="hover-sound-7" src="./assets/audio/hover_sound_7.mp3" preload="auto"></audio>
    <audio id="hover-sound-8" src="./assets/audio/hover_sound_8.mp3" preload="auto"></audio>

    <!-- Préchargement de l'image -->
    <img id="sky" src="/assets/blue-black-sky-with-stars.jpg">
  </a-assets>

    <template v-if="allAssetsLoaded">
      <a-sky src="#sky"></a-sky>
      <a-entity light="type: point; intensity: 2000" position="0 1000 0"></a-entity>
      <a-box
        color="red"
        position="7 1 7"
        scale="0.5 0.5 0.5"
        sound="src: #crazy-frog; on: click1"
        toggle-event
        pause-sound="event: click2"
        clickable
      ></a-box>

      <a-entity id="sound-container" position="0 0 0">
        <a-entity id="sound-hidden" sound="src: #hover-sound-hidden; positional: false; volume: 1"></a-entity>
        <a-entity id="sound-1" sound="src: #hover-sound-1; positional: false; loop: true; volume: 1"></a-entity>
        <a-entity id="sound-2" sound="src: #hover-sound-2; positional: false; loop: true; volume: 1"></a-entity>
        <a-entity id="sound-3" sound="src: #hover-sound-3; positional: false; loop: true; volume: 1"></a-entity>
        <a-entity id="sound-4" sound="src: #hover-sound-4; positional: false; loop: true; volume: 1"></a-entity>
        <a-entity id="sound-5" sound="src: #hover-sound-5; positional: false; loop: true; volume: 1"></a-entity>
        <a-entity id="sound-6" sound="src: #hover-sound-6; positional: false; loop: true; volume: 1"></a-entity>
        <a-entity id="sound-7" sound="src: #hover-sound-7; positional: false; loop: true; volume: 1"></a-entity>
        <a-entity id="sound-8" sound="src: #hover-sound-8; positional: false; loop: true; volume: 1"></a-entity>
      </a-entity>

      <MinesweeperGrid />

    </template>

    <TheCameraRig />

  </a-scene>
</template>