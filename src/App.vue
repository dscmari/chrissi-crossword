

<template>
    <div class="container">
        <div v-if="isSmallScreen" class="info-screen">
          <p>Crossword Puzzles are no fun on a small screen.</p>
          <p>Try a bigger screen!</p>
        </div>
        <h1>Chrissi Crossword</h1>
        <Canvas />
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

.container{
  position: relative;
}

.info-screen{
  position: fixed;
  width: 80%;
  height: 50%;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
  background-color: red;
  z-index: 100;
  display: flex;
  flex-direction: column;
  justify-content: center;
  color: white;
  font-size: 2.5rem;
  padding: 1rem;
}

h1 {
  color: white;
  font-family: 'Comic Sans MS', cursive, sans-serif;
}

</style>