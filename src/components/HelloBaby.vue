<template>
  <div class="page-container">
    <canvas id="renderCanvas" height="770" width="1900"></canvas>
    <!--     <div>
      水箱水位:<a-input
        v-model:value="waterPosition"
        @change="changeWater"
        style="width: 200px"
      />
    </div> -->
  </div>
</template>

<script setup>
import * as BABYLON from "@babylonjs/core/Legacy/legacy";
import "@babylonjs/loaders/legacy/legacy";
import * as GUI from "@babylonjs/gui/legacy/legacy";
import { onMounted } from "vue";
import { ref } from "vue";
const waterPosition = ref(0);
let label1 = "";
onMounted(() => {
  // Get the canvas DOM element
  //获得画布
  var canvas = document.getElementById("renderCanvas");
  var engine = new BABYLON.Engine(canvas, true, {
    preserveDrawingBuffer: true,
    stencil: true,
  });
  var scene = new BABYLON.Scene(engine);

  // 添加一个相机，并绑定鼠标事件
  var camera = new BABYLON.ArcRotateCamera(
    "Camera",
    Math.PI / 2,
    Math.PI / 2,
    2,
    BABYLON.Vector3.Zero(),
    scene
  );
  camera.setTarget(new BABYLON.Vector3(-14.56, 1.75, 5.25));
  camera.attachControl(canvas, true);
  // 添加2个灯光到场景中
  var light1 = new BABYLON.HemisphericLight(
    "light1",
    new BABYLON.Vector3(1, 1, 0),
    scene
  );
  light1.intensity = 1;
  var light2 = new BABYLON.PointLight(
    "light2",
    new BABYLON.Vector3(0, 1, -1),
    scene
  );
  var sphere1 = BABYLON.Mesh.CreateSphere("Sphere1", 32, 3, scene);
  sphere1.position.x = -5;
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
/*   function changeWater(e) {
    console.log(waterPosition.value);
    label1.text = "水箱水位：" + waterPosition.value;
  } */
</script>

<style></style>
