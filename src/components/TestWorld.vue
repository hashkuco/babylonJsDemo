<template>
  <div class="page-container">
    <canvas id="renderCanvas" height="770" width="1900"></canvas>
  </div>
</template>

<script setup>
import * as BABYLON from "@babylonjs/core/Legacy/legacy";
import "@babylonjs/loaders/legacy/legacy";
/* import * as GUI from "@babylonjs/gui/legacy/legacy"; */
import { onMounted } from "vue";
onMounted(() => {
  // Get the canvas DOM element
  //获得画布
  var canvas = document.getElementById("renderCanvas");
  // Load the 3D engine 加载
  var engine = new BABYLON.Engine(canvas, true, {
    preserveDrawingBuffer: true,
    stencil: true,
  });
  // This creates a basic Babylon Scene object (non-mesh) 设置场景
  var scene = new BABYLON.Scene(engine);
  scene.clearColor = new BABYLON.Color3(0.0823, 0.1289, 0.1921, 0.2);
  const camera = new BABYLON.ArcRotateCamera(
    "camera",
    -Math.PI / 2,
    Math.PI / 2.5,
    2,
    new BABYLON.Vector3(0, 0, 0)
  );
  camera.attachControl(canvas, true);
  const light = new BABYLON.HemisphericLight(
    "light",
    new BABYLON.Vector3(1, 1, 0)
  );
  light.intensity = 5;
  BABYLON.SceneLoader.ImportMeshAsync("", "./models/", "阀门开关.babylon").then(
    () => {
      const wheelRB = scene.getMeshByName("jiezhifakaiguan1");
      scene.beginAnimation(wheelRB, 0, 30, true);
    }
  );
  // Register a render loop to repeatedly render the scene
  //加载刷新
  engine.runRenderLoop(function () {
    scene.render();
  });

  // Watch for browser/canvas resize events
  window.addEventListener("resize", function () {
    engine.resize();
  });
});
</script>

<style></style>
