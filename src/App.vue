<!-- 
  @Author: sudongpeng
  @Date:   2025-06-11 09:39:49
  @Last Modified by:   sudongpeng
  @Last Modified time: 2025-06-16 11:02:56
-->
<script setup>
import { onBeforeUnmount, onMounted, reactive } from 'vue';
import LiquidSVG from './components/LiquidSVG.vue';
import * as dat from 'dat.gui';
const gui = new dat.GUI();
const testParam = reactive({
  width: 300,
  height: 300,
  mode: "standard",
  aberrationIntensity: 2,
  displacementScale: 24,
})

onMounted(() => {
  const folder = gui.addFolder('LiquidSVG');
  folder.add(testParam, 'width', 100, 800);
  folder.add(testParam, 'height', 100, 800);
  folder.add(testParam, 'mode', ["standard", "polar", "prominent", "shader"]);
  folder.add(testParam, 'aberrationIntensity', 0, 10);
  folder.add(testParam, 'displacementScale', 0, 100);
  folder.open();
})

onBeforeUnmount(() => {
  gui.destroy();
})


</script>

<template>
  <div class="container">
    <LiquidSVG
      :mode="testParam.mode"
      :width="testParam.width"
      :height="testParam.height"
      :aberrationIntensity="testParam.aberrationIntensity"
      :displacementScale="testParam.displacementScale"></LiquidSVG>
  </div>

</template>

<style scoped>
.container {
  width: 100%;
  height: 100%;
  background: url('@/assets/bg.jpg');
  background-size: 100%;
  display: flex;
  justify-content: center;
  align-items: center;
  overflow: hidden;
}
</style>
