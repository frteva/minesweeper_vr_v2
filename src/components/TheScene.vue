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
    <audio id="birds" src="./assets/audio/birds.wav" preload="auto"></audio>


    <!-- Préchargement de l'image -->
    <img id="sky" src="/assets/field_background_2.jpg">
    <img id="light_red" src="/assets/light_red.png" preload="auto">
    <img id="light_green" src="/assets/light_green.png" preload="auto">
    <img id="light_blue" src="/assets/light_blue.png" preload="auto">
    <img id="light_purple" src="/assets/light_purple.png" preload="auto">
    <img id="light_transparent" src="/assets/light_transparent.png" preload="auto">
    <img id="numbers" src="/assets/numbers.png" preload="auto">
    <img id="cases_rules" src="/assets/cases_rules.png" preload="auto">
    <img id="rules" src="/assets/rules.png" preload="auto">
    <img id="wasted" src="/assets/wasted.png" preload="auto">
    <img id="die" src="/assets/die_background.png" preload="auto">
    <a-asset-item id="field" src="./assets/field_and_garden.glb" preload="auto"></a-asset-item>
    <a-asset-item id="board" src="./assets/notice_board_low-poly.glb" preload="auto"></a-asset-item>
    <a-asset-item id="board2" src="./assets/notice_board.glb" preload="auto"></a-asset-item>
    <a-asset-item id="button" src="./assets/button.glb" preload="auto"></a-asset-item>
    <a-asset-item id="explosion" src="./assets/timeframe_explosion.glb" preload="auto"></a-asset-item>
  </a-assets>

    <template v-if="allAssetsLoaded">
      <a-sky src="#sky" rotation="0 -90 0"></a-sky>
      <a-entity light="type: point; intensity: 2000" position="0 1000 0"></a-entity>
      <a-entity light="type: point; intensity: 30" position="10 10 0"></a-entity>

      <a-entity 
        gltf-model="#field"
        position="5 3.55 -1" 
        scale="1 1 1"
        rotation="1 4 0"
      ></a-entity>

      <a-entity 
        gltf-model="#board2"
        position="-2 -0.8 1.55" 
        scale="1.4 1.4 1.4"
        rotation="0 90 0"
      ></a-entity>

      <a-entity clickable gltf-model="#button" outline-on-event position="-1.9 1.4 0.5" rotation="0 0 -90" scale="0.01 0.01 0.01" toggle-event="event: click; event1: click1; event2: click2" sound="src: #hover-sound-8; on: click1" pause-sound="event: click2"></a-entity>
      <a-entity clickable gltf-model="#button" outline-on-event position="-1.9 1.4 0.8" rotation="0 0 -90" scale="0.01 0.01 0.01" toggle-event="event: click; event1: click1; event2: click2" sound="src: #hover-sound-7; on: click1" pause-sound="event: click2"></a-entity>
      <a-entity clickable gltf-model="#button" outline-on-event position="-1.9 1.4 1.1" rotation="0 0 -90" scale="0.01 0.01 0.01" toggle-event="event: click; event1: click1; event2: click2" sound="src: #hover-sound-6; on: click1" pause-sound="event: click2"></a-entity>
      <a-entity clickable gltf-model="#button" outline-on-event position="-1.9 1.4 1.4" rotation="0 0 -90" scale="0.01 0.01 0.01" toggle-event="event: click; event1: click1; event2: click2" sound="src: #hover-sound-5; on: click1" pause-sound="event: click2"></a-entity>
      <a-entity clickable gltf-model="#button" outline-on-event position="-1.9 1.4 1.7" rotation="0 0 -90" scale="0.01 0.01 0.01" toggle-event="event: click; event1: click1; event2: click2" sound="src: #hover-sound-4; on: click1" pause-sound="event: click2"></a-entity>
      <a-entity clickable gltf-model="#button" outline-on-event position="-1.9 1.4 2.0" rotation="0 0 -90" scale="0.01 0.01 0.01" toggle-event="event: click; event1: click1; event2: click2" sound="src: #hover-sound-3; on: click1" pause-sound="event: click2"></a-entity>
      <a-entity clickable gltf-model="#button" outline-on-event position="-1.9 1.4 2.3" rotation="0 0 -90" scale="0.01 0.01 0.01" toggle-event="event: click; event1: click1; event2: click2" sound="src: #hover-sound-2; on: click1" pause-sound="event: click2"></a-entity>
      <a-entity clickable gltf-model="#button" outline-on-event position="-1.9 1.4 2.6" rotation="0 0 -90" scale="0.01 0.01 0.01" toggle-event="event: click; event1: click1; event2: click2" sound="src: #hover-sound-1; on: click1" pause-sound="event: click2"></a-entity>

      <a-plane src="#numbers" width="2.16" height="0.15" position="-1.9 1.6 1.55" rotation="0 90 0" transparent="true" material="emissive: white; emissiveIntensity: 0.8;"></a-plane>
      <a-plane src="#cases_rules" width="2.38" height="1.64" scale="0.4 0.4 0.4" position="-1.9 2 0.9" rotation="0 90 0" transparent="true" roughness="2"></a-plane>
      <a-plane src="#rules" width="2.89" height="1.65" scale="0.4 0.4 0.4" position="-1.9 2 2.1" rotation="0 90 0" transparent="true" roughness="2"></a-plane>


      <a-entity id="birds" sound="src: #birds; positional: false; loop: true; volume: 1; autoplay: true"></a-entity>

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