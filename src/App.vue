<template>
  <!-- Loading Screen -->
  <div 
    v-if="isLoading" 
    class="fixed inset-0 z-50 flex items-center justify-center bg-black/50 backdrop-blur-md transition-all duration-500"
  >
    <div class="text-center">
          <circle 
            class="opacity-25" 
            cx="12" 
            cy="12" 
            r="10" 
            stroke="currentColor" 
            stroke-width="4"
          />
      </div>
      <div class="text-white text-xl font-bold loading-text">
        Loading... {{ loadingProgress }}%
      </div>
    </div>
  <!-- Main Content -->
  <main
    :class="{ 'page-blur': !isTabActive }"
    class="relative z-10 overflow-x-hidden transition-all duration-500"
  >
    <!-- Custom Cursor -->
    <div
      v-for="text in clickTexts"
      :key="text.id"
      class="click-text"
      :style="{ left: text.x + 'px', top: text.y + 'px' }"
    >
      {{ text.text }}
    </div>

    <div v-for="(particle, index) in trailParticles" 
       :key="particle.id"
       class="trail-star"
       :style="{ 
         left: particle.x + 'px', 
         top: particle.y + 'px',
         opacity: index / trailParticles.length,
         transform: `scale(${(index / trailParticles.length) * 0.8})`
       }">
    <svg viewBox="0 0 512 512" width="100%" height="100%">
      <path d="M512 255.1c0 11.34-7.406 20.86-18.44 23.64l-171.3 42.78l-42.78 171.1C276.7 504.6 267.2 512 255.9 512s-20.84-7.406-23.62-18.44l-42.66-171.2L18.47 279.6C7.406 276.8 0 267.3 0 255.1c0-11.34 7.406-20.83 18.44-23.61l171.2-42.78l42.78-171.1C235.2 7.406 244.7 0 256 0s20.84 7.406 23.62 18.44l42.78 171.2l171.2 42.78C504.6 235.2 512 244.6 512 255.1z"></path>
    </svg>
  </div>

    <div
      class="custom-cursor"
      :class="{ clicking: isClicking }"
      :style="{
        left: cursorPos.x + 'px',
        top: cursorPos.y + 'px',
        transform: `translate(0, 0) scale(${isClicking ? 0.8 : 1})`,
      }"
    >
      <img src="./Normal.cur" alt="cursor" class="cursor-image" />
      <div class="cursor-dot"></div>
      <div class="cursor-ring"></div>
    </div>

    <!-- Floating Icons -->
    <div
      class="floating-icon top-0 right-0 text-[10rem] opacity-10 z-0"
      aria-hidden="true"
    >
      ❄
    </div>
    <div
      class="floating-icon bottom-0 left-0 text-[10rem] opacity-10 z-0"
      aria-hidden="true"
    >
      ✨
    </div>
    <div
      class="floating-icon top-1/4 left-1/3 text-[8rem] opacity-10 z-0"
      aria-hidden="true"
    >
      🌙
    </div>
    <div
      class="floating-icon bottom-1/4 right-1/4 text-[8rem] opacity-10 z-0"
      aria-hidden="true"
    >
      🌹
    </div>

    <div id="app" class="relative mb-16">
      <Header />
      <snowf />
      <SnowRGB />

      <Body />
    </div>

    <footer
      class="relative z-10 bg-primary text-white py-6 rounded-lg shadow-lg"
    >
      <Footer />
    </footer>
  </main>
</template>

<script>
import Body from "./components/Body.vue";
import Footer from "./components/Footer.vue";
import Header from "./components/Header.vue";
import Snowf from "./components/Snowf.vue";
import SnowRGB from "./components/SnowRGB.vue";

export default {
  components: {
    Header,
    Body,
    Footer,
    Snowf,
    SnowRGB
  },
  data() {
    return {
      cursorPos: { x: 0, y: 0 },
      isClicking: false,
      isLinkHover: false,
      isTabActive: true,
      isLoading: true,
      loadingProgress: 0,
      loadingInterval: null,
      clickTexts: [],
      randomTexts: [
        "????",
        "yay!",
        "!!!!!",
        "Your star!!",
        "✦✦✦",
        "★★★★★",
        "wowwww!",
        "click!",
        "poppp!",
        "Coey",
        "Winterfell",
        "Ypur cool",
        "eieioden",
        "i don't know",
      ],
      trailParticles: [],
      maxTrailLength: 20,
      textColors: [
        'rgb(147, 51, 234)', // Purple
        'rgb(236, 72, 153)', // Pink  
        'rgb(59, 130, 246)', // Blue
      ]
    };
  },
  mounted() {
    window.addEventListener("mousemove", this.updateCursorPosition);
    window.addEventListener("mousedown", () => (this.isClicking = true));
    window.addEventListener("mouseup", () => (this.isClicking = false));
    window.addEventListener("click", this.spawnClickText);
    window.addEventListener("scroll", this.handleScroll);
    this.startLoading();

    document.addEventListener("keydown", (e) => {
      if (
        e.key === "F12" ||
        (e.ctrlKey && e.shiftKey && e.key === "I") ||
        (e.ctrlKey && e.shiftKey && e.key === "J") ||
        (e.ctrlKey && e.shiftKey && e.key === "C") ||
        (e.ctrlKey && e.key === "U")
      ) {
        e.preventDefault();
      }
    });
    setInterval(() => {
      const devtools = /./;
      devtools.toString = function () {
        this.opened = true;
      };
      console.log("%c", devtools);
    }, 1000);
    document.addEventListener("visibilitychange", this.handleVisibilityChange);
    document.addEventListener("contextmenu", (e) => e.preventDefault());
    document.querySelectorAll("a, button").forEach((el) => {
      el.addEventListener("mouseenter", () => (this.isLinkHover = true));
      el.addEventListener("mouseleave", () => (this.isLinkHover = false));
    });
  },
  beforeUnmount() {
    window.removeEventListener("mousemove", this.updateCursorPosition);
    window.removeEventListener("mousedown", () => (this.isClicking = true));
    window.removeEventListener("mouseup", () => (this.isClicking = false));
    window.removeEventListener("click", this.spawnClickText);
    document.removeEventListener(
      "visibilitychange",
      this.handleVisibilityChange
    );
    if (this.loadingInterval) {
      clearInterval(this.loadingInterval);
    }
    window.removeEventListener("scroll", this.handleScroll);

    document.querySelectorAll("a, button").forEach((el) => {
      el.removeEventListener("mouseenter", () => (this.isLinkHover = true));
      el.removeEventListener("mouseleave", () => (this.isLinkHover = false));
    });
  },
  methods: {
    updateCursorPosition(event) {
      this.lastX = event.clientX;
      this.lastY = event.clientY;
      this.cursorPos.x = this.lastX;
      this.cursorPos.y = this.lastY + window.scrollY;

      this.trailParticles.push({
        id: Date.now(),
        x: this.lastX,
        y: this.lastY + window.scrollY,
      });

      if (this.trailParticles.length > this.maxTrailLength) {
        this.trailParticles.shift();
      }
    },
    handleScroll() {
      this.scrollY = window.scrollY;
      this.cursorPos.y = this.lastY + this.scrollY;

      this.trailParticles = this.trailParticles.map((particle) => ({
        ...particle,
        y: particle.y + (this.scrollY - this.lastScrollY),
      }));
      this.lastScrollY = this.scrollY;
    },

    // Add these new methods
    startLoading() {
      this.loadingProgress = 0;
      this.isLoading = true;
      
      this.loadingInterval = setInterval(() => {
        if (this.loadingProgress < 100) {
          this.loadingProgress += Math.floor(Math.random() * 10) + 1;
          if (this.loadingProgress > 100) {
            this.loadingProgress = 100;
          }
        } else {
          clearInterval(this.loadingInterval);
          this.finishLoading();
        }
      }, 200);

      setTimeout(() => {
        if (this.loadingProgress < 100) {
          this.loadingProgress = 100;
          this.finishLoading();
        }
      }, 3000);
    },

    finishLoading() {
      this.isLoading = false;
      if (this.loadingInterval) {
        clearInterval(this.loadingInterval);
      }
    },

    spawnClickText(event) {
      const text = this.randomTexts[Math.floor(Math.random() * this.randomTexts.length)]
      const randomColor = Math.floor(Math.random() * this.textColors.length)
      this.clickTexts.push({
        id: Date.now(),
        text,
        x: event.clientX,
        y: event.clientY + window.scrollY,
        color: this.textColors[randomColor]
      })
      setTimeout(() => {
        this.clickTexts = this.clickTexts.filter(t => t.id !== text.id)
      }, 1000)
    },
    handleVisibilityChange() {
      this.isTabActive = document.visibilityState === "visible";
    },
  },
};
</script>

<style scoped>


* {
  -webkit-user-select: none;
  -moz-user-select: none;
  -ms-user-select: none;
  user-select: none;
  -ms-overflow-style: none;  
  scrollbar-width: none;  
}

*::-webkit-scrollbar {
  display: none;
  width: 0;
  background: transparent;
}

html {
  scroll-behavior: smooth;
  overflow-y: scroll;
  scrollbar-width: none; 
  -ms-overflow-style: none;
}

body {
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: none;
  -ms-overflow-style: none;
}

.trail-star {
  position: fixed;
  width: 15px;
  height: 15px;
  pointer-events: none;
  transform: translate(-50%, -50%);
  z-index: 9998;
  animation: rotateStar 2s linear infinite, colorCycle 3s linear infinite;
}

.trail-star svg {
  width: 100%;
  height: 100%;
  fill: currentColor;
  filter: drop-shadow(0 0 8px currentColor);
}

@keyframes rotateStar {
  from { transform: translate(-50%, -50%) rotate(0deg); }
  to { transform: translate(-50%, -50%) rotate(360deg); }
}

@keyframes colorCycle {
  0% {
    color: rgb(147, 51, 234);
    filter: drop-shadow(0 0 8px rgba(147, 51, 234, 0.8));
  }
  33% {
    color: rgb(236, 72, 153);
    filter: drop-shadow(0 0 8px rgba(236, 72, 153, 0.8));
  }
  66% {
    color: rgb(59, 130, 246);
    filter: drop-shadow(0 0 8px rgba(59, 130, 246, 0.8));
  }
  100% {
    color: rgb(147, 51, 234);
    filter: drop-shadow(0 0 8px rgba(147, 51, 234, 0.8));
  }
}



.trail-particle {
  position: fixed;
  width: 12px;
  height: 12px;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%, -50%);
  mix-blend-mode: difference;
  transition: all 0.1s ease;
  box-shadow: 0 0 15px 5px rgba(255, 255, 255, 0.5),
    0 0 30px 15px rgba(255, 255, 255, 0.3),
    0 0 45px 25px rgba(255, 255, 255, 0.1);
  animation: pulseGlow 2s ease-in-out infinite;
}

@keyframes pulseGlow {
  0%,
  100% {
    box-shadow: 0 0 15px 5px rgba(255, 255, 255, 0.5),
      0 0 30px 15px rgba(255, 255, 255, 0.3),
      0 0 45px 25px rgba(255, 255, 255, 0.1);
  }

  50% {
    box-shadow: 0 0 20px 8px rgba(255, 255, 255, 0.6),
      0 0 40px 20px rgba(255, 255, 255, 0.4),
      0 0 60px 30px rgba(255, 255, 255, 0.2);
  }
}

* {
  cursor: none !important;
  transition: transform 0.3s ease, filter 0.3s ease, opacity 0.3s ease;
}

main {
  transition: all 0.5s cubic-bezier(0.4, 0, 0.2, 1);
  will-change: transform, opacity, filter;
  overflow-y: auto;
  -webkit-overflow-scrolling: touch;
  scrollbar-width: none;
  -ms-overflow-style: none;
}

#app {
  overflow: hidden;
  -ms-overflow-style: none;
  scrollbar-width: none;
}

#app::-webkit-scrollbar {
  display: none;
  width: 0;
  background: transparent;
}

.page-blur {
  filter: blur(10px);
  opacity: 0.7;
  transform: scale(0.98);
  pointer-events: none;
}

.page-transition-enter-active {
  animation: fadeIn 0.5s ease-out;
}

.page-transition-leave-active {
  animation: fadeOut 0.5s ease-in;
}

.custom-cursor {
  position: fixed;
  pointer-events: none;
  z-index: 9999;
  width: 32px;
  height: 32px;
  transform: translate(-50%, -50%);
  will-change: transform;
}

.cursor-image {
  width: 100%;
  height: 100%;
  object-fit: contain;
}

.clicking .cursor-image {
  transform: scale(0.8);
  transition: transform 0.1s ease;
}

.cursor-glow {
  position: fixed;
  width: 20px;
  height: 20px;
  background: rgba(255, 255, 255, 0.8);
  border-radius: 50%;
  pointer-events: none;
  transform: translate(-50%, -50%);
  z-index: 9998;
  mix-blend-mode: difference;
  transition: all 0.1s ease;
  box-shadow: 0 0 15px 5px rgba(255, 255, 255, 0.5),
    0 0 30px 15px rgba(255, 255, 255, 0.3),
    0 0 45px 25px rgba(255, 255, 255, 0.1);
  will-change: transform;
}

.click-text {
  position: absolute;
  pointer-events: none;
  animation: floatUp 1s ease-out forwards, textGlow 2s linear infinite;
  font-weight: bold;
  z-index: 9999;
  transform: translate(-50%, -50%);
  color: white;
  font-size: 1.5rem;
}

.loading-text {
  animation: pulse 2s cubic-bezier(0.4, 0, 0.6, 1) infinite;
  background: linear-gradient(
    to right,
    rgb(147, 51, 234),
    rgb(236, 72, 153),
    rgb(59, 130, 246)
  );
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  background-size: 200% auto;
}

@keyframes pulse {
  0%, 100% {
    opacity: 1;
    transform: scale(1);
  }
  50% {
    opacity: .7;
    transform: scale(0.95);
  }
}

@keyframes textGlow {
  0% {
    color: rgb(147, 51, 234);
    text-shadow: 0 0 10px rgba(147, 51, 234, 0.8),
                 0 0 20px rgba(147, 51, 234, 0.4),
                 0 0 30px rgba(147, 51, 234, 0.2);
  }
  33% {
    color: rgb(236, 72, 153);
    text-shadow: 0 0 10px rgba(236, 72, 153, 0.8),
                 0 0 20px rgba(236, 72, 153, 0.4),
                 0 0 30px rgba(236, 72, 153, 0.2);
  }
  66% {
    color: rgb(59, 130, 246);
    text-shadow: 0 0 10px rgba(59, 130, 246, 0.8),
                 0 0 20px rgba(59, 130, 246, 0.4),
                 0 0 30px rgba(59, 130, 246, 0.2);
  }
  100% {
    color: rgb(147, 51, 234);
    text-shadow: 0 0 10px rgba(147, 51, 234, 0.8),
                 0 0 20px rgba(147, 51, 234, 0.4),
                 0 0 30px rgba(147, 51, 234, 0.2);
  }
}

/* Floating icons */
.floating-icon {
  position: fixed;
  pointer-events: none;
  animation: float 8s ease-in-out infinite;
  filter: drop-shadow(0 0 15px rgba(255, 255, 255, 0.2));
  opacity: 0.1;
  transition: all 0.3s ease;
}

.floating-icon:nth-child(2n) {
  animation-delay: -4s;
  animation-duration: 10s;
}

.floating-icon:nth-child(3n) {
  animation-delay: -2s;
  animation-duration: 12s;
}

.floating-icon:nth-child(4n) {
  animation-delay: -6s;
  animation-duration: 14s;
}

header,
section,
footer {
  max-width: 100%;
  padding: 1.5rem;
}

header {
  text-align: center;
}

footer {
  text-align: center;
  font-weight: bold;
  border-top: 2px solid rgba(255, 255, 255, 0.2);
}

.hover-glow:hover {
  filter: drop-shadow(0 0 15px rgba(255, 255, 255, 0.3));
  transform: translateY(-2px);
}

@keyframes floatUp {
  0% {
    opacity: 1;
    transform: translate(-50%, -50%) scale(0.5);
  }
  50% {
    opacity: 1;
    transform: translate(-50%, -100%) scale(1.2);
  }
  100% {
    opacity: 0;
    transform: translate(-50%, -150%) scale(1);
  }
}

@keyframes float {
  0%,
  100% {
    transform: translate(0, 0) rotate(0deg);
    filter: drop-shadow(0 0 15px rgba(255, 255, 255, 0.1));
  }

  25% {
    transform: translate(10px, -15px) rotate(5deg);
    filter: drop-shadow(0 0 20px rgba(255, 255, 255, 0.2));
  }

  50% {
    transform: translate(0, -25px) rotate(0deg);
    filter: drop-shadow(0 0 25px rgba(255, 255, 255, 0.3));
  }

  75% {
    transform: translate(-10px, -15px) rotate(-5deg);
    filter: drop-shadow(0 0 20px rgba(255, 255, 255, 0.2));
  }
}

@keyframes fadeIn {
  from {
    opacity: 0;
    filter: blur(10px);
    transform: scale(0.95);
  }

  to {
    opacity: 1;
    filter: blur(0);
    transform: scale(1);
  }
}

@keyframes fadeOut {
  from {
    opacity: 1;
    filter: blur(0);
    transform: scale(1);
  }

  to {
    opacity: 0;
    filter: blur(10px);
    transform: scale(0.95);
  }
}
</style>
