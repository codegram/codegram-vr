<template>
  <canvas ref="canvas" style="position: absolute; top: 0; left: 0; height: 100vh; width: 100vw;" />
</template>

<script lang="ts">
import { defineComponent } from 'vue'
import HelloWorld from './components/HelloWorld.vue'

import { Engine, Scene, Vector3, ArcRotateCamera, HemisphericLight, MeshBuilder, SceneLoader, AbstractMesh, PBRMetallicRoughnessMaterial, Color3 } from "babylonjs";
import "babylonjs-loaders"
import codegramObject from "./assets/codegram.gltf.json"

export default defineComponent({
  name: 'App',
  components: {
    HelloWorld
  },
  async mounted() {
    let canvas = this.$refs.canvas as HTMLCanvasElement
    let engine = new Engine(canvas, true)
    const scene = new Scene(engine)

    const importedScene = await SceneLoader.AppendAsync("", `data:${JSON.stringify(codegramObject)}`, scene)
    let logo = importedScene.meshes[1]
    var pbr = new PBRMetallicRoughnessMaterial("pbr", scene);
    logo.material = pbr

    pbr.baseColor = new Color3(229 / 255, 85 / 255, 79 / 255);
    pbr.metallic = 0.9;
    pbr.roughness = 0.15;

    const camera = new ArcRotateCamera("camera", -Math.PI, Math.PI / 3, 1.5, new Vector3(0, 1, 0), scene);
    let helperLight = scene.createDefaultLight();
    let helper = scene.createDefaultEnvironment({});
    const light = new HemisphericLight("light", new Vector3(0, 1, 0), scene);
    camera.attachControl(canvas, true);

    const xr = scene.createDefaultXRExperienceAsync({
      floorMeshes: [helper?.ground as AbstractMesh],
    });

    var renderLoop = function () {
        scene.render();
    };

    engine.runRenderLoop(renderLoop);
  }
})
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>