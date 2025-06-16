<!-- 
  @Author: sudongpeng
  @Date:   2025-06-11 09:39:49
  @Last Modified by:   sudongpeng
  @Last Modified time: 2025-06-16 11:02:56
-->
<script setup>
import { onBeforeUnmount, onMounted, reactive, ref } from 'vue';
import LiquidSVG from './components/LiquidSVG.vue';
import * as dat from 'dat.gui';


const gui = new dat.GUI();
const params = reactive({
  width: 300,
  height: 300,
  mode: "standard",
  aberrationIntensity: 2,
  displacementScale: 24,
  blurAmount: 1,
  saturation: 100
})

const transform = ref('translate(0,0)')

const onMouseMove = (e) => {

  // 获取鼠标位置
  const x = e.clientX
  const y = e.clientY
  transform.value = `translate(${x - (params.width / 2)}px, ${y - (params.height / 2)}px)`
}

onMounted(() => {
  window.addEventListener('mousemove', onMouseMove)
  const folder = gui.addFolder('LiquidSVG');
  folder.add(params, 'width', 100, 800);
  folder.add(params, 'height', 100, 800);
  folder.add(params, 'mode', ["standard", "polar", "prominent", "shader"]);
  folder.add(params, 'aberrationIntensity', 0, 10);
  folder.add(params, 'displacementScale', 0, 100);
  folder.add(params, 'blurAmount', 0, 20);
  folder.add(params, 'saturation', 0, 200);
  folder.open();
})

onBeforeUnmount(() => {
  gui.destroy();
})


</script>

<template>
  <div class="container">
    <LiquidSVG
      ref="liquidSvg"
      :style="{ position: 'absolute', top: 0, left: 0, transform: transform }"
      :mode="params.mode"
      :width="params.width"
      :height="params.height"
      :aberrationIntensity="params.aberrationIntensity"
      :displacementScale="params.displacementScale"
      :blurAmount="params.blurAmount"
      :saturation="params.saturation"></LiquidSVG>
  </div>

</template>

<style scoped>
.container {
  position: relative;
  width: 100%;
  height: 100%;
  background: url('@/assets/bg.jpg');
  background-size: 100%;
  overflow: hidden;
}
</style>
