<template>
  <li class="frosted-glass-wrapper" @click="$emit('qualify', movie)">
    <div
      class="frosted-glass"
      :class="side"
      :style="`background: URL('http://image.tmdb.org/t/p/w780/${movie.backdrop_path}'); background-size: cover; background-repeat: no-repeat; background-position: center;`"
    >
      <div
        class="flex items-center justify-center bg-slate-800 self-end rounded-lg p-2 text-white text-xs font-bold"
      >
        {{ movie.title }}
      </div>
    </div>
  </li>
</template>

<style>
.frosted-glass-wrapper {
  display: grid;
  place-content: center;
  inset: 0;
  transform-style: preserve-3d;
  perspective: 800px;
  cursor: pointer;
}
.frosted-glass {
  display: flex;
  padding: 0.5rem;
  border-radius: 0.75rem;
  height: 30vh;
  width: clamp(20rem, 40vw, 28rem);
  background-size: cover;
  background-color: #f9f7f7;
  backdrop-filter: blur(10px);
  box-shadow: 50px 50px 30px rgba(0, 0, 0, 0.3);
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
@media (max-width: 1164px) {
  .frosted-glass {
    max-height: 12rem;
  }
}
.left {
  transition: all 0.5s;
  transform: rotateX(10deg) rotateY(10deg);
}
.left:hover {
  transition: all 0.5s;
  z-index: 2;
  transform: scale(1.1) rotateX(10deg) rotateY(10deg);
}
.right {
  justify-content: flex-end;
  transition: all 0.5s;
  transform: rotateX(10deg) rotateY(-10deg);
}
.right:hover {
  transition: all 0.5s;
  z-index: 2;
  transform: scale(1.1) rotateX(10deg) rotateY(-10deg);
}
</style>

<script setup lang="ts">
import type { Movie } from "~/types";

defineProps<{
  side: "left" | "right";
  movie: Movie;
}>();

defineEmits<{
  (e: "qualify", value: Movie): void;
}>();
</script>
