<script setup>
import '../aframe/disable-in-vr.js';
import '../aframe/hide-in-vr.js';
import '../aframe/simple-navmesh-constraint.js';
import '../aframe/blink-controls.js';
import '../aframe/physx-grab.js';
import { onMounted } from 'vue';
import { markdownPressed, isDead } from '../store/pad.js';
import Keyboard from '../utils/keyboard.js';

onMounted(() => {

  const keyboard = new Keyboard();
  const handRight = document.querySelector("#hand-right");

  if (!handRight) {
    console.error("🚨 `hand-right` introuvable !");
    return;
  }

  console.log("✅ `hand-right` trouvé :", handRight);

  // 🎮 Écoute le `abuttondown` sur la manette
  handRight.addEventListener("abuttondown", () => {
    markdownPressed.value = true;
  });

  handRight.addEventListener("abuttonup", () => {
    markdownPressed.value = false;
  });

  handRight.addEventListener("gripdown", () => {
    markdownPressed.value = true;
  });

  handRight.addEventListener("gripup", () => {
    markdownPressed.value = false;
  });

  keyboard.onKey("KeyM", () => {
    markdownPressed.value = true;
  });

  keyboard.onKey("KeyM", () => {
    markdownPressed.value = false;
  }, "up");
});
</script>

<template>
  <a-entity
    id="camera-rig"
    movement-controls="camera: #head;"
    disable-in-vr="component: movement-controls;"
  >

    <a-entity
      id="head"
      look-controls="pointerLockEnabled: true"
      simple-navmesh-constraint="navmesh: [data-role='nav-mesh']; height: 1.65;"
      disable-in-vr="component: simple-navmesh-constraint;"
      camera
      position="0 1.65 0"
    >
      <a-entity light="type: point; intensity: 4" position="0 0 0"></a-entity>
      <a-plane v-if="isDead" src="#wasted" width="1" height="1" scale="5 5 5" position="0 0 -3" rotation="0 0 0" transparent="true" roughness="2"></a-plane>
      <a-plane v-if="isDead" src="#die" width="1.920" height="1.080" scale="5 5 5" position="0 0 -3" rotation="0 0 0" transparent="true" roughness="2"></a-plane>

      <a-plane ref="wastedRef"
        src="#wasted"
        width="1" height="1"
        scale="5 5 5"
        position="0 0 -3"
        rotation="0 0 0"
        transparent="true"
        roughness="2"
        visible="false"
      ></a-plane>

      <a-plane ref="dieRef"
        src="#die"
        width="1.920" height="1.080"
        scale="5 5 5"
        position="0 0 -3"
        rotation="0 0 0"
        transparent="true"
        roughness="2"
        visible="false"
      ></a-plane>

    
      <a-entity
        geometry="primitive: circle; radius: 0.0003;"
        material="shader: flat; color: white;"
        cursor
        raycaster="far: 4; objects: [clickable]; showLine: false;"
        position="0 0 -0.1"
        disable-in-vr="component: raycaster; disableInAR: false;"
        hide-in-vr="hideInAR: false"
      ></a-entity>

      <a-entity id="dummy-hand-right" position="0.3 -0.4 -0.5"></a-entity>
      <a-entity id="dummy-hand-left" position="-0.3 -0.4 -0.5"></a-entity>
    </a-entity>

    <a-entity
      id="hand-left"
      hand-controls="hand: left"
      blink-controls="
        cameraRig: #camera-rig;
        teleportOrigin: #head;
        collisionEntities: [data-role='nav-mesh'];
        snapTurn: false;
      "
      position="0 1.5 0"
      physx-grab
    >
      <a-sphere id="hand-left-collider"
        radius="0.02"
        visible="false"
        physx-body="type: kinematic; emitCollisionEvents: true">
      </a-sphere>
    </a-entity>

    <a-entity
      id="hand-right"
      hand-controls="hand: right"
      laser-controls="hand: right"
      raycaster="far: 4; objects: [clickable]; showLine: true;"
      position="0 1.5 0"
      physx-grab
    >
      <a-sphere id="hand-right-collider"
        radius="0.02"
        visible="false"
        physx-body="type: kinematic; emitCollisionEvents: true">
      </a-sphere>
    </a-entity>

  </a-entity>
</template>
