<template>
  <div class="page-container">
    <canvas id="renderCanvas" height="770" width="1900"></canvas>
    <div>
      水箱水位:<a-input
        v-model:value="waterPosition"
        @change="changeWater"
        style="width: 200px"
      />
    </div>
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
  // Load the 3D engine 加载
  var engine = new BABYLON.Engine(canvas, true, {
    preserveDrawingBuffer: true,
    stencil: true,
  });
  // This creates a basic Babylon Scene object (non-mesh) 设置场景
  var scene = new BABYLON.Scene(engine);
  scene.clearColor = new BABYLON.Color3(0.0823, 0.1289, 0.1921, 0.2);
  //創建相机
  var camera = new BABYLON.ArcRotateCamera(
    "camera1",
    2.026,
    2.0605,
    34.9979,
    new BABYLON.Vector3(0, 0, 0),
    scene
  );
  // This targets the camera to scene origin
  camera.setTarget(new BABYLON.Vector3(-14.56, 1.75, 5.25));

  // This attaches the camera to the canvas
  camera.attachControl(canvas, true);
  camera.useFramingBehavior = true;
  var light = new BABYLON.HemisphericLight(
    "light",
    new BABYLON.Vector3(0, 1, 0),
    scene
  );
  light.intensity = 5;
  var animationBox = new BABYLON.Animation(
    "myAnimation",
    "rotation.y",
    30,
    BABYLON.Animation.ANIMATIONTYPE_FLOAT,
    BABYLON.Animation.ANIMATIONLOOPMODE_CYCLE
  );
  var keys = [];

  //第一个键，第0帧，缩放值为1
  keys.push({
    frame: 0,
    value: 0,
  });

  //在动画第20帧时，缩放值为 0.2
  keys.push({
    frame: 50,
    value: Math.PI,
  });

  //在第100帧时，缩放值为1
  keys.push({
    frame: 100,
    value: 2 * Math.PI,
  });
  let meshs = {};
  animationBox.setKeys(keys);
  BABYLON.SceneLoader.LoadAssetContainer(
    "./models/",
    "esite1.gltf",
    scene,
    function (container) {
      let mesh = {};
      let actionMesh = {};
      let zhutiMesh = {};
      //container.createRootMesh().scaling = new BABYLON.Vector3(0.003, 0.003, 0.003);
      for (let i = 0; i < container.meshes.length; i++) {
        if (container.meshes[i].id == "截止阀.040_primitive0") {
          console.log(container.meshes[i]);
          mesh = container.meshes[i];
        }
        if (container.meshes[i].id == "水箱") {
          actionMesh = container.meshes[i];
        }
        if (container.meshes[i].id == "柱体.037_primitive0") {
          zhutiMesh = container.meshes[i];
        }
        if (container.meshes[i].id == "jiezhifakaiguan1") {
          meshs = container.meshes[i];
          meshs.animations = [];
          meshs.animations.push(animationBox);
          console.log("meshs:", meshs);
          scene.beginAnimation(meshs, 0, 100, true);
        }
      }
      var advancedTexture = GUI.AdvancedDynamicTexture.CreateFullscreenUI("UI");
      advancedTexture.idealWidth = 600;

      var rect1 = new GUI.Rectangle();
      rect1.width = 0.1;
      rect1.height = "10px";
      rect1.cornerRadius = 20;
      rect1.color = "Orange";
      rect1.thickness = 4;
      rect1.background = "#ffee11";
      advancedTexture.addControl(rect1);
      rect1.linkWithMesh(mesh);
      rect1.linkOffsetY = -40;

      var label = new GUI.TextBlock();
      label.text = "截止阀40";
      rect1.addControl(label);
      var line = new GUI.Line();
      line.lineWidth = 4;
      line.color = "Orange";
      line.y2 = 20;
      line.linkOffsetY = -30;
      advancedTexture.addControl(line);
      line.linkWithMesh(mesh);
      line.connectedControl = rect1;
      container.addAllToScene();

      var rect2 = new GUI.Rectangle();
      rect2.width = 0.1;
      rect2.height = "10px";
      rect2.cornerRadius = 20;
      rect2.color = "#aaffdd";
      rect2.thickness = 4;
      rect2.background = "#0099ff";
      advancedTexture.addControl(rect2);
      rect2.linkWithMesh(actionMesh);
      rect2.linkOffsetY = -40;
      label1 = new GUI.TextBlock();
      label1.text = "水箱水位" + waterPosition.value;
      rect2.addControl(label1);
      rect2.isVisible = 0;
      actionMesh.actionManager = new BABYLON.ActionManager(scene);
      actionMesh.actionManager.registerAction(
        new BABYLON.InterpolateValueAction(
          BABYLON.ActionManager.OnPickTrigger,
          rect2,
          "isVisible",
          1
        )
      );
      var material = new BABYLON.StandardMaterial("kosh", scene);
      material.diffuseColor = new BABYLON.Color3(0, 0, 0); //漫反射颜色
      material.emissiveColor = new BABYLON.Color3(0, 0.789, 0.894); //自发光颜色
      material.alpha = 0.4; //材质透明度
      material.specularPower = 16;
      material.emissiveFresnelParameters = new BABYLON.FresnelParameters();
      material.emissiveFresnelParameters.bias = 0.3;
      material.emissiveFresnelParameters.power = 16;
      material.emissiveFresnelParameters.leftColor = BABYLON.Color3.White();
      material.emissiveFresnelParameters.rightColor = BABYLON.Color3.Black();
      material.opacityFresnelParameters = new BABYLON.FresnelParameters();
      material.opacityFresnelParameters.leftColor = BABYLON.Color3.White();
      material.opacityFresnelParameters.rightColor = BABYLON.Color3.Black();
      zhutiMesh.material = material;
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
function changeWater(e) {
  console.log(waterPosition.value);
  label1.text = "水箱水位：" + waterPosition.value;
}
</script>

<style></style>
