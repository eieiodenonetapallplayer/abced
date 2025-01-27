<template>
  <header
    class="group sticky top-4 z-30 mb-16 box-content w-full rounded-xl border border-transparent py-3 px-4 transition-all duration-300 hover:shadow-lg sm:rounded-2xl [&.not-top]:border-border [&.not-top]:bg-background/95 dark:[&.not-top]:bg-primary-foreground/95 backdrop-blur-sm"
  >
    <h1
      class="flex-none text-xl font-bold transition-all duration-300 group-[.not-top]:ms-3 sm:group-[.not-top]:ms-5"
    >
      <span class="magic group" @mousemove="updateStarPositions">
        <span
          v-for="(star, index) in stars"
          :key="index"
          class="magic-star"
          :style="{
            '--star-left': `${star.left}%`,
            '--star-top': `${star.top}%`,
            '--animation-delay': `${index * 0.1}s`,
          }"
        >
          <svg viewBox="0 0 512 512" class="group-hover:opacity-100">
            <path
              d="M512 255.1c0 11.34-7.406 20.86-18.44 23.64l-171.3 42.78l-42.78 171.1C276.7 504.6 267.2 512 255.9 512s-20.84-7.406-23.62-18.44l-42.66-171.2L18.47 279.6C7.406 276.8 0 267.3 0 255.1c0-11.34 7.406-20.83 18.44-23.61l171.2-42.78l42.78-171.1C235.2 7.406 244.7 0 256 0s20.84 7.406 23.62 18.44l42.78 171.2l171.2 42.78C504.6 235.2 512 244.6 512 255.1z"
            ></path>
          </svg>
        </span>
        <span class="magic-text group-hover:animate-text-shimmer"
          >COEYONETAP \ Home</span
        >
      </span>
    </h1>
  </header>
</template>

<script setup>
import { ref, onMounted, onUnmounted } from "vue";

const stars = ref([
  { left: 60, top: -20 },
  { left: 80, top: 30 },
  { left: 20, top: 50 },
]);

const updateStarPositions = () => {
  stars.value = stars.value.map(() => ({
    left: Math.random() * 100,
    top: Math.random() * 100 - 20,
  }));
};

const handleScroll = () => {
  const header = document.querySelector("header");
  if (window.scrollY > 0) {
    header.classList.add("not-top");
  } else {
    header.classList.remove("not-top");
  }
};

onMounted(() => {
  window.addEventListener("scroll", handleScroll);
});

onUnmounted(() => {
  window.removeEventListener("scroll", handleScroll);
});
</script>

<style scoped>
.magic {
  display: inline-block;
  position: relative;
}

.magic-star {
  --size: 1.3rem;
  animation: star 1.1s ease-in-out infinite;
  animation-delay: var(--animation-delay);
  display: block;
  height: var(--size);
  left: var(--star-left);
  position: absolute;
  top: var(--star-top);
  width: var(--size);
  opacity: 0;
  transition: all 0.3s ease;
  filter: drop-shadow(0 0 10px currentColor);
}

.magic-star > svg {
  animation: rotate 1s linear infinite, colorChange 3s linear infinite;
  display: block;
  opacity: 0.7;
  fill: rgb(var(--star-color, 255, 255, 255));
  filter: drop-shadow(0 0 8px rgb(var(--star-color, 255, 255, 255, 0.8)));
}

@keyframes colorChange {
  0% {
    --star-color: 147, 51, 234;
  }
  33% {
    --star-color: 236, 72, 153;
  }
  66% {
    --star-color: 59, 130, 246;
  }
  100% {
    --star-color: 147, 51, 234;
  }
}

.magic-text {
  background: linear-gradient(to right, #9333ea, #ec4899, #3b82f6, #9333ea);
  -webkit-background-clip: text;
  background-clip: text;
  color: transparent;
  background-size: 200% auto;
  transition: all 0.3s ease;
}

.animate-text-shimmer {
  animation: textShimmer 8s linear infinite;
  background-size: 500% auto;
}

@keyframes star {
  0% {
    transform: translate(calc(Math.random() * 20px), calc(Math.random() * 20px))
      scale(0);
    opacity: 0;
    filter: drop-shadow(0 0 0 currentColor);
  }
  50% {
    transform: translate(0, 0) scale(1);
    opacity: 1;
    filter: drop-shadow(0 0 15px currentColor);
  }
  100% {
    transform: translate(
        calc(Math.random() * -20px),
        calc(Math.random() * -20px)
      )
      scale(0);
    opacity: 0;
    filter: drop-shadow(0 0 0 currentColor);
  }
}

@keyframes rotate {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(180deg);
  }
}

@keyframes textShimmer {
  0% {
    background-position: -500% 0;
  }
  100% {
    background-position: 500% 0;
  }
}
</style>
