<script setup>
import { ref, onMounted } from 'vue';
const commit = ref(null);
const loading = ref(true);
const error = ref(null);
const currentYear = new Date().getFullYear();
const currentTime = ref(new Date().toLocaleTimeString());
let timeInterval;

onMounted(async () => {
  try {
    loading.value = true;
    const response = await fetch('https://api.github.com/repos/4levy/website/commits');
    if (!response.ok) throw new Error('Failed to fetch commit data');
    const data = await response.json();
    commit.value = data[0];
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
  timeInterval = setInterval(() => {
    currentTime.value = new Date().toLocaleTimeString();
  }, 1000);
});
</script>

<template>
  <div class="container mx-auto px-4">
    <div class="grid grid-cols-1 md:grid-cols-3 gap-4 md:gap-6 py-4 md:py-8">
      <div class="flex items-center justify-center md:justify-start space-x-2 text-sm text-catppuccin-gray hover:text-white transition-colors">
        <font-awesome-icon :icon="['fas', 'code-branch']" class="mt-[3px]" />
        <div v-if="loading">Loading commit data...</div>
        <div v-else-if="error">{{ error }}</div>
        <a
          v-else-if="commit"
          :href="`https://github.com/4levy/website/commit/${commit.sha}`"
          target="_blank"
          rel="noopener noreferrer"
          class="hover:underline truncate max-w-[200px] md:max-w-full"
        >
          {{ commit.sha.slice(0, 7) }} — {{ commit.commit.message }}
        </a>
      </div>
      <div class="text-center text-sm text-catppuccin-gray order-first md:order-none">
        <p class="mb-1">{{ currentTime }}</p>
      </div>
      <div class="text-center md:text-right text-sm text-catppuccin-gray">
        <p>&copy; {{ currentYear }} COEYSTEPTHEP. All rights reserved.</p>
      </div>
    </div>
  </div>
  <footer class="bg-catppuccin-mantle text-catppuccin-text text-center p-4 footer-blur">
    <div class="flex justify-center items-center mt-4">
      <img
        src="https://count.getloli.com/@4levy?name=4levy&theme=booru-qualityhentais&padding=7&offset=0&scale=1&pixelated=1&darkmode=auto"
        alt="Visitor Count"
        class="max-w-[250px] md:max-w-[300px] w-full h-auto mx-auto"
      />
    </div>
  </footer>
</template>

<style scoped>
.container {
  max-width: 1200px;
}

a {
  text-decoration: none;
  transition: all 0.2s ease;
}

a:hover {
  transform: translateY(-2px);
}

@media (max-width: 768px) {
  .grid {
    row-gap: 1rem;
  }
  
  a {
    display: inline-block;
    white-space: nowrap;
    overflow: hidden;
    text-overflow: ellipsis;
  }
}
</style>