<script setup>
import { ref } from 'vue';

const domRef = ref(null);
</script>

<template>
  <div class="liquid-glass"></div>
</template>

<style lang="css" scoped>
.liquid-glass {
  width: 300px;
  height: 300px;
  border-radius: 20px;
  background: rgba(255, 255, 255, 0.2);
  box-shadow:
    0 8px 32px rgba(104, 104, 104, 0.37),
    inset 0 4px 4px rgba(255, 255, 255, 0.3);
  border: 1px solid rgba(255, 255, 255, 0.18);
  position: relative;
  overflow: hidden;
  backdrop-filter: blur(5px) brightness(1.2);
}


.liquid-glass::before {
  content: '';
  position: absolute;
  width: 150%;
  height: 150%;
  background: linear-gradient(45deg,
      rgba(255, 255, 255, 0) 0%,
      rgba(255, 255, 255, 0.3) 50%,
      rgba(255, 255, 255, 0) 100%);
  top: -25%;
  left: -25%;
  transform: rotate(45deg);
  backdrop-filter: blur(0px);
}
</style>
