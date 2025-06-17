<!-- 
  @Author: sudongpeng
  @Date:   2025-06-11 10:19:48
  @Last Modified by:   sudongpeng
  @Last Modified time: 2025-06-16 09:48:02
-->
<script setup lang="ts">
import { displacementMap, polarDisplacementMap, prominentDisplacementMap, } from "./lib/utils.ts"
import { ref, computed, watch } from 'vue'

type Mode = "standard" | "polar" | "prominent" | "shader";
interface LiquidGlassProps {
  width?: number,
  height?: number,
  displacementScale?: number
  blurAmount?: number
  saturation?: number
  aberrationIntensity?: number
  elasticity?: number
  cornerRadius?: number
  globalMousePos?: { x: number; y: number }
  mouseOffset?: { x: number; y: number }
  className?: string
  padding?: string
  overLight?: boolean
  mode?: Mode
  onClick?: () => void
}

const props = withDefaults(defineProps<LiquidGlassProps>(), {
  width: 300,
  height: 300,
  mode: 'standard',
  aberrationIntensity: 2,
  displacementScale: 25,
  blurAmount: 0,
  saturation: 120,
});


const overlayRef = ref<any>(null)
const width = computed(() => props.width ?? 300);
const height = computed(() => props.height ?? 300);
const aberrationIntensity = computed(() => props.aberrationIntensity ?? 2);
const displacementScale = computed(() => props.displacementScale ?? 25)
const mode = computed(() => props.mode ?? 'standard');
const blurAmount = computed(() => props.blurAmount ?? 0);
const saturation = computed(() => props.saturation ?? 120);
const shaderMapUrl = ref(undefined)
const filterKey = ref(0);
watch(
  () => [displacementScale.value, mode.value, aberrationIntensity.value],
  () => {
    filterKey.value += 1;
  }
);
const id = computed(() => `$lquidGlass_${filterKey.value}`)

const getMap = (mode: Mode, shaderMapUrl?: string) => {
  switch (mode) {
    case "standard":
      return displacementMap
    case "polar":
      return polarDisplacementMap
    case "prominent":
      return prominentDisplacementMap
    case "shader":
      return shaderMapUrl || displacementMap
    default:
      throw new Error(`Invalid mode: ${mode}`)
  }
}

</script>

<template>
  <div class="liquid-svg">
    <svg style="position: absolute;" :width="width" :height="height" aria-hidden="true">
      <defs>
        <radialGradient :id="`${id}-edge-mask`" cx="50%" cy="50%" r="50%">
          <stop offset="0%" stopColor="black" stopOpacity="0" />
          <stop :offset="`${Math.max(30, 80 - aberrationIntensity * 2)}%`" stopColor="black" stopOpacity="0" />
          <stop offset="100%" stopColor="white" stopOpacity="1" />
        </radialGradient>
        <filter :key="filterKey" :id="id" x="-35%" y="-35%" width="170%" height="170%" colorInterpolationFilters="sRGB">
          <feImage id="feimage" x="0" y="0" width="100%" height="100%" result="DISPLACEMENT_MAP" :href="getMap(mode,
            shaderMapUrl)" preserveAspectRatio="xMidYMid slice" />

          <!-- {/* Create edge mask using the displacement map itself */} -->
          <feColorMatrix
            in="DISPLACEMENT_MAP"
            type="matrix"
            values="0.3 0.3 0.3 0 0
                 0.3 0.3 0.3 0 0
                 0.3 0.3 0.3 0 0
                 0 0 0 1 0"
            result="EDGE_INTENSITY" />
          <feComponentTransfer in="EDGE_INTENSITY" result="EDGE_MASK">
            <feFuncA type="discrete" :tableValues="`0 ${aberrationIntensity * 0.05} 1`" />
          </feComponentTransfer>

          <!-- {/* Original undisplaced image for center */} -->
          <feOffset in="SourceGraphic" dx="0" dy="0" result="CENTER_ORIGINAL" />

          <!-- {/* Red channel displacement with slight offset */} -->
          <feDisplacementMap in="SourceGraphic" in2="DISPLACEMENT_MAP" :scale="displacementScale * (mode === 'shader' ? 1 :
            -1)" xChannelSelector="R" yChannelSelector="B" result="RED_DISPLACED" />
          <feColorMatrix
            in="RED_DISPLACED"
            type="matrix"
            values="1 0 0 0 0
                 0 0 0 0 0
                 0 0 0 0 0
                 0 0 0 1 0"
            result="RED_CHANNEL" />

          <!-- {/* Green channel displacement */} -->
          <feDisplacementMap in="SourceGraphic" in2="DISPLACEMENT_MAP" :scale="displacementScale * ((mode === 'shader' ? 1 :
            -1) - aberrationIntensity * 0.05)" xChannelSelector="R" yChannelSelector="B" result="GREEN_DISPLACED" />
          <feColorMatrix
            in="GREEN_DISPLACED"
            type="matrix"
            values="0 0 0 0 0
                 0 1 0 0 0
                 0 0 0 0 0
                 0 0 0 1 0"
            result="GREEN_CHANNEL" />

          <!-- {/* Blue channel displacement with slight offset */} -->
          <feDisplacementMap in="SourceGraphic" in2="DISPLACEMENT_MAP" :scale="displacementScale * ((mode === 'shader' ? 1 :
            -1) - aberrationIntensity * 0.1)" xChannelSelector="R" yChannelSelector="B" result="BLUE_DISPLACED" />
          <feColorMatrix
            in="BLUE_DISPLACED"
            type="matrix"
            values="0 0 0 0 0
                 0 0 0 0 0
                 0 0 1 0 0
                 0 0 0 1 0"
            result="BLUE_CHANNEL" />

          <!-- {/* Combine all channels with screen blend mode for chromatic aberration */} -->
          <feBlend in="GREEN_CHANNEL" in2="BLUE_CHANNEL" mode="screen" result="GB_COMBINED" />
          <feBlend in="RED_CHANNEL" in2="GB_COMBINED" mode="screen" result="RGB_COMBINED" />

          <!-- {/* Add slight blur to soften the aberration effect */} -->
          <feGaussianBlur in="RGB_COMBINED" :stdDeviation="Math.max(0.1, 0.5 - aberrationIntensity * 0.1)"
            result="ABERRATED_BLURRED" />

          <!-- {/* Apply edge mask to aberration effect */} -->
          <feComposite in="ABERRATED_BLURRED" in2="EDGE_MASK" operator="in" result="EDGE_ABERRATION" />

          <!-- {/* Create inverted mask for center */} -->
          <feComponentTransfer in="EDGE_MASK" result="INVERTED_MASK">
            <feFuncA type="table" tableValues="1 0" />
          </feComponentTransfer>
          <feComposite in="CENTER_ORIGINAL" in2="INVERTED_MASK" operator="in" result="CENTER_CLEAN" />

          <!-- {/* Combine edge aberration with clean center */} -->
          <feComposite in="EDGE_ABERRATION" in2="CENTER_CLEAN" operator="over" />
        </filter>
      </defs>
    </svg>
    <div class="overlay"
      :style="{ width: `${width}px`, height: `${height}px`, backdropFilter: `url(#${id}) blur(${blurAmount}px) saturate(${saturation}%)` }"
      ref="overlayRef"></div>
  </div>

</template>



<style scoped>
.liquid-svg {
  width: auto;
  height: auto;
}

.overlay {
  position: relative;
  top: 0;
  left: 0;
  box-shadow:
    0 8px 32px rgba(104, 104, 104, 0.1),
    inset 0 10px 10px rgba(255, 255, 255, 0.4);
  border-left: 1px solid rgba(255, 255, 255, 0.18);
  border-radius: 20px;
  margin: 0 20px;
}
</style>