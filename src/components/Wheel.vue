<script setup>
import { ref, computed } from "vue"
import bgImg from "../assets/img/BG.jpg"
import drumImg from "../assets/img/Roulette.png"
import btnImg from "../assets/img/button-img-main_1.png"
import btnImgActive from "../assets/img/button-img-active.webp"
import foxLeft from "../assets/img/Fox.png"
import raccoonRight from "../assets/img/Racoon.png"

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
   <!-- 💰 Блок JACKPOT -->
    <div class="jackpot-box">
      <div class="jackpot-title">JACKPOT</div>
      <div class="jackpot-value">18 158 518 €</div>
    </div>
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
.jackpot-box {
  position: absolute;
  top: 0vh;
  left: 50%;
  transform: translateX(-50%);
  background: #000;
  border: 3px solid #FFD700;
  border-radius: 12px;
  padding: 8px 20px;
  text-align: center;
  color: #fff;
  font-family: sans-serif;
  z-index: 5;
  box-shadow: 0 0 15px rgba(255,215,0,.7);
}
.jackpot-title {
  font-size: 14px;
  font-weight: 700;
  letter-spacing: 1px;
}
.jackpot-value {
  font-size: 20px;
  font-weight: 900;
  color: #FFD700;
}
.app-bg {
  position: relative;
  width: 100%;
  height: 105vh;
  display: flex;
  align-items: center;
  justify-content: center;
  overflow: visible;
}
.bg-img {
  position: absolute; inset: 0; width: 100%; height: 100%;
  object-fit: cover; z-index: 0;
}
/* .left-hero {
  position: absolute; bottom: 0px; height: 75vh;
  object-fit: contain; z-index: 1; pointer-events: none;
}
.right-hero {
  position: absolute; bottom: 0px; height: 60vh;
  object-fit: contain; z-index: 1; pointer-events: none;
}
.left-hero { left: 0px; } .right-hero { right: 0px; } */
.left-hero, .right-hero {
  position: absolute;
  bottom: 0;               /* привязка к низу */
  height: 40vw;            /* масштаб от ширины экрана */
  max-height: 80vh;        /* ограничение по высоте */
  object-fit: contain;
  z-index: 1;
  pointer-events: none;
}

.left-hero {
  left: 2vw;               /* отступ в процентах */
}

.right-hero {
  right: 2vw;
}
.wheel-wrap {
  position: relative;
  width: min(85vw, 85vh); /* 📱 адаптивный размер */
  aspect-ratio: 1;        /* всегда квадрат */
  margin-inline: auto;
  margin-top: 5vh;        /* можно регулировать отступ сверху */
}
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

/* --- 📺 Desktop (по умолчанию) --- */

/* --- 📱 Tablet (768px - 1199px) --- */
/* @media (max-width: 1199px) {
  .left-hero {
    height: 70vh;
    left: 40px;
    bottom: -100px;
  }
  .right-hero {
    height: 70vh;
    right: 40px;
    bottom: -100px;
  }
  .wheel-wrap {
    margin-top: 9vh;
  }
  .jackpot-box {
    font-size: 0.9rem;
    padding: 6px 16px;
  }
  .jackpot-value {
    font-size: 18px;
  }
} */

/* --- 📱 Mobile (до 768px) --- */
/* @media (max-width: 768px) { */
  /* .left-hero {
    display: none;
  } */
  /* .right-hero {
    height: 55vh;
    right: 10px;
    bottom: -60px;
  }
  .wheel-wrap {
    margin-top: 6vh;
  }
  .jackpot-box {
    top: 2vh;
    padding: 5px 14px;
  }
  .jackpot-title {
    font-size: 12px;
  }
  .jackpot-value {
    font-size: 16px;
  }
} */

/* --- 📱 Extra-small (до 480px) --- */
/* @media (max-width: 480px) {
  .left-hero, .right-hero {
  position: absolute;
  bottom: 0;               /* привязка к низу */
  /* height: 40vw;            /* масштаб от ширины экрана */
  /* max-height: 80vh;        ограничение по высоте */
  /* object-fit: contain; */
  /* z-index: 1; */
  /* pointer-events: none; */
/* } */ 

/* .left-hero {
  left: 2vw;               /* отступ в процентах */
/* }

.right-hero {
  right: 2vw;
} */ 
  /* .wheel-wrap {
    margin-top: -9vh;
    width: min(100vw, 100vh);
    right: 1.7vh;
  }
  .jackpot-box {
    padding: 4px 10px;
  }
  .jackpot-value {
    font-size: 14px;
  } */
/* } */

@media (max-width: 1024px) {
  .left-hero, .right-hero {
    height: 35vw;
  }
}

@media (max-width: 768px) {
  .left-hero {
    height: 30vw;
    left: 1vw;
  }
  .right-hero {
    height: 32vw;
    right: 1vw;
  }
}

@media (max-width: 480px) {
  .left-hero, .right-hero {
    height: 28vw;   /* ещё меньше */
  }
}
</style>
