<template>
  <div class="winner-wrapper">
    <h2 class="winner">Winner</h2>
    <div>
      <div
        class="frosted-glass winner-card"
        :style="`background: URL('http://image.tmdb.org/t/p/w780/${winner.backdrop_path}'); background-size: cover; background-repeat: no-repeat; background-position: center;`"
      >
        <div
          class="flex items-center justify-center bg-slate-800 self-end rounded-lg p-2 text-white text-xs font-bold"
        >
          {{ winner.title }}
        </div>
      </div>
    </div>
  </div>
</template>

<style>
.winner-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
}
.winner {
  font-size: 4rem;
  color: #f9f7f7;
}
.frosted-glass {
  display: flex;
  padding: 0.5rem;
  border-radius: 0.75rem;
  height: 30vh;
  width: 40vw;
  max-width: 28rem;
  background-size: cover;
  background-color: #f9f7f7;
  backdrop-filter: blur(10px);
  box-shadow: 50px 50px 30px rgba(0, 0, 0, 0.3);
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
.winner-card {
  transform: scale(1.2);
}
</style>

<script setup lang="ts">
import type { Movie } from "~/types";

defineProps<{
  winner: Movie;
}>();
</script>
