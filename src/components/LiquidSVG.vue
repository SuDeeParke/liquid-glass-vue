<template>
  <svg width="0" height="0">
    <defs>
      <filter
        x="-50%"
        y="-50%"
        width="200%"
        height="200%"
        color-interpolation-filters="sRGB"
        id="liquidGlass">

        <!-- 放大 -->
        <feMorphology in="SourceGraphic" operator="in" radius="0.45" result="zoomed" />


        <feComponentTransfer in="zoomed" result="brightened">
          <feFuncR type="linear" slope="0.8" intercept="0.1" />
          <feFuncG type="linear" slope="0.8" intercept="0.1" />
          <feFuncB type="linear" slope="0.8" intercept="0.1" />
        </feComponentTransfer>

        <!-- 噪点 -->
        <feTurbulence
          type="turbulence"
          baseFrequency="0.0005 0.005"
          numOctaves="1"
          seed="20"
          result="turbulenceNoise" />
        <feGaussianBlur in="turbulenceNoise" stdDeviation="10 20" result="softMap" />

        <!-- 扭曲  -->
        <feDisplacementMap
          in="brightened"
          in2="softMap"
          result="displaced"
          scale="-100"
          xChannelSelector="R"
          yChannelSelector="G" />

        <!-- 光照 -->
        <feSpecularLighting in="displaced" specularExponent="10" specularConstant="1" result="spec">
          <fePointLight x="0" y="0" z="100" />
        </feSpecularLighting>
        <feComposite in="displaced" in2="spec" operator="arithmetic" k1="1" k2="1" k3="1" k4="0" />


      </filter>
    </defs>

  </svg>
  <div class="overlay" ref="overlayRef"></div>
</template>

<script setup>
import { ref, onMounted } from 'vue'

const overlayRef = ref(null)

const onMouseMove = (e) => {
  const overlay = overlayRef.value
  if (!overlay) return

  // 获取鼠标位置
  const x = e.clientX
  const y = e.clientY

  // 设置元素位置，使鼠标位于中心点（-150 是宽度/高度的一半）
  overlay.style.transform = `translate(${x - 150}px, ${y - 150}px)`
}

onMounted(() => {
  window.addEventListener('mousemove', onMouseMove)
})
</script>

<style scoped>
.overlay {
  position: absolute;
  top: 0;
  left: 0;
  width: 300px;
  height: 300px;

  backdrop-filter: url(#liquidGlass) blur(1px);
  box-shadow:
    0 8px 32px rgba(104, 104, 104, 0.1),
    inset 0 10px 10px rgba(255, 255, 255, 0.4);
  border-left: 1px solid rgba(255, 255, 255, 0.18);
  border-radius: 100%;
  margin: 0 20px;
}
</style>