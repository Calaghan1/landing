<script setup>
import { ref, computed } from "vue"
import bgImg from "../assets/img/fon.jpg"
import drumImg from "../assets/img/drum.webp"
import btnImg from "../assets/img/button-img-main.webp"
import btnImgActive from "../assets/img/button-img-active.webp"
import foxLeft from "../assets/img/lis.png"
import raccoonRight from "../assets/img/raccoon.png"

// Каждый сектор — массив строк [{ text, size }]
const sectors = [
  [ { text: "100%", size: 6, color: "#000", dy: 0 },   { text: "на депозит", size: 2.2, color: "#000", dy: 2 } ],
  [ { text: "50", size: 6, color: "#fff", dy: 0 },     { text: "FS", size: 3, color: "#fff", dy: 1.5 } ],
  [ { text: "MAXBIT", size: 4.2, color: "#000", dy: 0 },    { text: "jackpot", size: 2.6, color: "#000", dy: 1.5 } ],
  [ { text: "попробуй", size: 2.0, color: "#fff", dy: 0 }, { text: "еще раз", size: 2.2, color: "#fff", dy: 1.5 } ],
  [ { text: "250", size: 6, color: "#000", dy: 0 },    { text: "FS", size: 4, color: "#000", dy: 1.2 } ],
  [ { text: "150%", size: 5, color: "#fff", dy: 0 },   { text: "на депозит", size: 2.2, color: "#fff", dy: 2 } ],
  [ { text: "500", size: 6, color: "#000", dy: 0 },    { text: "FS", size: 4, color: "#000", dy: 1.2 } ],
  [ { text: "MAXBIT", size: 4.2, color: "#fff", dy: 0 },    { text: "jackpot", size: 2.6, color: "#fff", dy: 1.5 } ],
  [ { text: "попробуй", size: 2.0, color: "#000", dy: 0 }, { text: "еще раз", size: 2.2, color: "#000", dy: 1.5 } ],
  [ { text: "100", size: 6, color: "#fff", dy: 0 },    { text: "FS", size: 4, color: "#fff", dy: 1.2 } ],
]

const rotation = ref(0)
const spinning = ref(false)
const sectorAngle = computed(() => 360 / sectors.length)
const rText = 33 // радиус для центра надписей (в % viewBox)

function spin() {
  if (spinning.value) return
  spinning.value = true
  const sectorAngleLocal = 360 / sectors.length
  const randomIndex = Math.floor(Math.random() * sectors.length)
  const targetRotation = 360 * 5 + (360 - randomIndex * sectorAngleLocal - sectorAngleLocal / 2)
  rotation.value = targetRotation
  setTimeout(() => {
    spinning.value = false
    const label = sectors[randomIndex].map(l => l.text).join(" ")
    alert("Выпало: " + label)
  }, 5000)
}
</script>

<template>
  <div class="app-bg">
    <img :src="bgImg" class="bg-img" alt="Background" />
    <img :src="foxLeft" class="left-hero" alt="Hero Left" />
    <img :src="raccoonRight" class="right-hero" alt="Hero Right" />

    <div class="wheel-wrap">
      <div class="pointer">
        <svg viewBox="0 0 24 24" fill="currentColor">
          <path d="M12 2L22 22H2L12 2z"/>
        </svg>
      </div>

      <div class="wheel" :style="{ transform: `rotate(${rotation}deg)` }">
        <img :src="drumImg" class="wheel-img" alt="Колесо фортуны" />

        <!-- SVG-оверлей с надписями -->
        <svg class="wheel-overlay" viewBox="0 0 100 100" preserveAspectRatio="xMidYMid meet">
          <g transform="translate(50,50)">
            <g
              v-for="(sector, i) in sectors"
              :key="i"
              :transform="`rotate(${i * sectorAngle})`"
            >
              <text
                :x="0"
                :y="-(rText)"
                text-anchor="middle"
                dominant-baseline="middle"
                class="wheel-label"
              >
                <!-- Каждая строка — свой размер -->
                <tspan
                  v-for="(line, j) in sector"
                  :key="j"
                  x="0"
                  :dy="line.dy + 'em'"
                  :style="{
                    fontSize: line.size + 'px',
                    fill: line.color,
                    strokeWidth: (line.size * 0.16).toFixed(2) + 'px'
                  }"
                >
                  {{ line.text }}
                </tspan>
              </text>
            </g>
          </g>
        </svg>
      </div>

      <img
        :src="spinning ? btnImgActive : btnImg"
        class="spin-btn"
        alt="Spin"
        @click="spin"
      />
    </div>
  </div>
</template>

<style scoped>
.app-bg {
  position: relative;
  width: 100%;
  height: 100vh;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: hidden;
}
.bg-img {
  position: absolute; inset: 0; width: 100%; height: 100%;
  object-fit: cover; z-index: 0;
}
.left-hero, .right-hero {
  position: absolute; bottom: -120px; height: 90vh;
  object-fit: contain; z-index: 1; pointer-events: none;
}
.left-hero { left: 90px; } .right-hero { right: 90px; }

.wheel-wrap { position: relative; width: 100%; aspect-ratio: 1; max-width: 570px; margin-inline: auto; }
.wheel { position: relative; width: 100%; height: 100%; border-radius: 50%;
  transition: transform cubic-bezier(.2,.8,.15,1) 5s; }
.wheel-img { position: absolute; inset: 0; width: 100%; height: 100%; border-radius: 50%; }

.wheel-overlay { position: absolute; inset: 0; width: 100%; height: 100%; pointer-events: none; }

/* Базовый стиль: цвет/обводка/жирность; размер задаётся индивидуально в :style */
.wheel-label {
  font-weight: 800;
  paint-order: stroke;
  stroke: rgba(0,0,0,.1);
  letter-spacing: .3px;
  text-transform: uppercase;
}

.spin-btn {
  position: absolute; top: 50%; left: 50%;
  transform: translate(-37%, -44%);
  width: 25%; height: auto; cursor: pointer; z-index: 3;
}
.pointer { position: absolute; top: -8px; left: 50%; transform: translateX(-50%); z-index: 4; }
.pointer svg { width: 36px; height: 36px; }
</style>
