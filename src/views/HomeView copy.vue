<template>
  <canvas ref="canvasRef"></canvas>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue"
import { OrbitControls } from "three/examples/jsm/controls/OrbitControls"
import {BoxLineGeometry} from "three/examples/jsm/geometries/BoxLineGeometry"
import {VRButton} from "three/examples/jsm/webxr/VRButton"
import {XRControllerModelFactory} from "three/examples/jsm/webxr/XRControllerModelFactory"
import * as THREE from "three"

let scene = new THREE.Scene()
scene.background = new THREE.Color(0xaaaaaa)
let renderer
let controls

let canvasRef = ref();

let controllers
const raycaster = new THREE.Raycaster()
const workingMatrix = new THREE.Matrix4()
const workingVector = new THREE.Vector3()



const random = (min, max) => {
  return Math.random() * (max - min) + min;
}

let camera = new THREE.PerspectiveCamera(
  60, //vertical field of view
  window.innerWidth / window.innerHeight, //aspect ratio
  0.1, //near plane
  100 //far plane
);
camera.position.y = 0;
camera.position.z = 4;
camera.position.x=0;
scene.add(camera);


const ambientLight = new THREE.HemisphereLight( 0x606060, 0x404040);
scene.add(ambientLight)

const dirLight = new THREE.DirectionalLight(0xffffff)
dirLight.position.set(1, 1, 1).normalize()
scene.add(dirLight)

let loop = () => {
  controls.update();

  

  renderer.render(scene, camera);
};

let resizeCallback = () => {
  camera.aspect = window.innerWidth / window.innerHeight;
  
  renderer.setSize(window.innerWidth, window.innerHeight);
  camera.updateProjectionMatrix();
};




onMounted(() => {
  renderer = new THREE.WebGLRenderer({
    canvas: canvasRef.value,
    antialias: true,
    alpha: true,
  });

  renderer.setSize(window.innerWidth, window.innerHeight);
  renderer.setPixelRatio(window.devicePixelRatio);
  renderer.render(scene, camera);

  renderer.xr.enabled = true

  renderer.setAnimationLoop(loop);

  document.body.appendChild(VRButton.createButton(renderer))

  controls = new OrbitControls(camera, canvasRef.value);
  controls.enableDamping = true;


  window.addEventListener("resize", resizeCallback);
});

onUnmounted(() => {
  renderer.setAnimationLoop(null);
});
</script>

<style>
  canvas {
    width: 100%;
    height: 100%;
    display: block;
  }
</style>