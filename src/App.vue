<template>
    <div>
        <h1>Chrissi Crossword</h1>
        <div v-if="isSmallScreen" class="info-screen">
          <p>Crossword Puzzles are no fun on a small screen.</p>
          <p>Try a bigger screen!</p>
        </div>
        <div v-else>
          <Canvas />
        </div>
    </div>
</template>

<script setup>
  import Canvas from './components/Canvas.vue';
  import { ref, onMounted, onBeforeUnmount } from 'vue';

  const isSmallScreen = ref(window.innerWidth <= 1024);

  onMounted(() => {
    window.addEventListener('resize', handleResize);
  });

  onBeforeUnmount(() => {
    window.removeEventListener('resize', handleResize);
  });

  function handleResize() {
    isSmallScreen.value = window.innerWidth <= 1024;
  }
</script>

<style>

.info-screen{
  color: white;
  font-size: 2rem;
  font-family: 'Comic Sans MS', cursive, sans-serif;
  padding: 1rem;
}

h1 {
  color: white;
  font-family: 'Comic Sans MS', cursive, sans-serif;
}

</style>