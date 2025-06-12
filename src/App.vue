<script setup>
import LiquidSVG from './components/LiquidSVG.vue';
</script>

<template>
  <div class="container">
    <LiquidSVG></LiquidSVG>
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
