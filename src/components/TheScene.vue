<script setup>
  import { ref, onMounted } from 'vue';

  import TheCameraRig from './TheCameraRig.vue';
  import BaseMine from './BaseMine.vue';
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
</script>

<template>
  <a-scene
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
    <a-asset-item id="crazy-frog" src="./assets/audio/Crazy-Frog-Axel-F.mp3" response-type="arraybuffer" preload="auto"></a-asset-item>
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

      <MinesweeperGrid />

    </template>

    <TheCameraRig />

  </a-scene>
</template>