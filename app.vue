<template>
  <div class="w-full h-full flex items-center justify-center wrapper">
    <div v-if="winner && currentStage === 0" class="winner-wrapper">
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

    <div class="flex flex-col items-center" v-else>
      <span class="round" v-if="currentStage === 8">8th finals</span>
      <span class="round" v-if="currentStage === 4">Quarter finals</span>
      <span class="round" v-if="currentStage === 2">Semi-finals</span>
      <span class="round finals" v-if="currentStage === 1">FINALS</span>

      <ul v-if="currentMatch?.contenders" class="flex items-center gap-8">
        <li
          class="frosted-glass-wrapper"
          @click="qualify(currentMatch.contenders[0])"
        >
          <div
            class="frosted-glass left-card"
            :style="`background: URL('http://image.tmdb.org/t/p/w780/${currentMatch.contenders[0].backdrop_path}'); background-size: cover; background-repeat: no-repeat; background-position: center;`"
          >
            <div
              class="flex items-center justify-center bg-slate-800 self-end rounded-lg p-2 text-white text-xs font-bold"
            >
              {{ currentMatch.contenders[0].title }}
            </div>
          </div>
        </li>
        <div class="versus">VS</div>
        <li
          class="frosted-glass-wrapper"
          @click="qualify(currentMatch.contenders[1])"
        >
          <div
            class="frosted-glass right-card"
            :style="`background: URL('http://image.tmdb.org/t/p/w780/${currentMatch.contenders[1].backdrop_path}'); background-size: cover; background-repeat: no-repeat; background-position: center;`"
          >
            <div
              class="flex items-center justify-center bg-slate-800 self-end rounded-lg p-2 text-white text-xs font-bold"
            >
              {{ currentMatch.contenders[1].title }}
            </div>
          </div>
        </li>
      </ul>
    </div>
  </div>
</template>

<style>
@import url("~/assets/app.css");
@import url("https://fonts.googleapis.com/css2?family=Luckiest+Guy&display=swap");

.wrapper {
}

.round {
  font-family: "Luckiest Guy", cursive;
  font-size: 1.4rem;
  color: #f9f7f7;
}

.round.finals {
  font-size: 2rem;
}

.winner-wrapper {
  display: flex;
  flex-direction: column;
  align-items: center;
  gap: 2rem;
}

.winner {
  font-family: "Luckiest Guy", cursive;
  font-size: 4rem;
  color: #f9f7f7;
}

.versus {
  font-family: "Luckiest Guy", cursive;
  font-size: 3rem;
  color: #ff204e;
}

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
  width: 40vw;
  aspect-ratio: 9 / 16;
  /* background: url("https://cdn.midjourney.com/f87ef132-b7e1-40a4-9e7c-0bf90e7e7724/0_3.png"); */
  background-size: cover;
  background-color: #f9f7f7;
  backdrop-filter: blur(10px);
  box-shadow: 50px 50px 30px rgba(0, 0, 0, 0.3);
}

.left-card {
  transition: all 0.5s;
  transform: rotateX(10deg) rotateY(10deg);
}

.left-card:hover {
  transition: all 0.5s;
  z-index: 2;
  transform: scale(1.1) rotateX(10deg) rotateY(10deg);
}

.right-card {
  justify-content: flex-end;
  transition: all 0.5s;
  transform: rotateX(10deg) rotateY(-10deg);
}

.right-card:hover {
  transition: all 0.5s;
  z-index: 2;
  transform: scale(1.1) rotateX(10deg) rotateY(-10deg);
}

.winner-card {
  transform: scale(1.2);
}
</style>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import type { Ref } from "vue";
import mock from "~/assets/mock.json";

console.log(mock);

interface Movie {
  adult: boolean;
  backdrop_path: string;
  genre_ids: number[];
  id: number;
  original_language: string;
  original_title: string;
  overview: string;
  popularity: number;
  poster_path: string;
  release_date: string;
  title: string;
  video: boolean;
  vote_average: number;
  vote_count: number;
}

interface Match {
  contenders: Movie[];
}

const movies: Ref<Movie[]> = ref([]);
const matches: Ref<Match[]> = ref([]);
const currentMatchIndex = ref(0);
const currentMatch: Ref<Match | null> = ref(null);
const currentStage = ref(8);
const currentStageQualifiers: Ref<Movie[]> = ref([]);
const winner: Ref<Movie | null> = ref(null);

function endGame() {
  currentStage.value = 0;
}

function qualify(movie: Movie) {
  if (currentStage.value === 1) {
    winner.value = movie;
    endGame();
  }

  const contendersTemp = {};
  currentStageQualifiers.value.push(movie);
  currentMatchIndex.value++;

  // Advance to next stage of the tournament
  if (currentMatchIndex.value >= currentStage.value) {
    currentStage.value = currentStage.value / 2;

    const tempMatches: Match[] = [];
    let matchId = 0;

    currentStageQualifiers.value.map((movie, index) => {
      if (index % 2 === 0) {
        tempMatches[matchId] = { contenders: [movie] };
      } else {
        tempMatches[matchId].contenders.push(movie);
        matchId++;
      }
    });

    matches.value = tempMatches;
    currentStageQualifiers.value = [];

    currentMatchIndex.value = 0;
  }

  currentMatch.value = matches.value[currentMatchIndex.value];
}

onMounted(async () => {
  // const res = await fetch("https://api.themoviedb.org/3/movie/top_rated", {
  //   method: "GET",
  //   headers: {
  //     accept: "application/json",
  //     Authorization:
  //       "Bearer ",
  //   },
  // });
  // movies.value = await res.json().results;

  const runtimeConfig = useRuntimeConfig();
  console.log(runtimeConfig.public.tmdbKey);

  const shuffledMovies = mock.results.slice(0, 16);

  // Shuffle the array
  for (var i = shuffledMovies.length - 1; i > 0; i--) {
    var j = Math.floor(Math.random() * (i + 1));
    var temp = shuffledMovies[i];
    shuffledMovies[i] = shuffledMovies[j];
    shuffledMovies[j] = temp;
  }

  const tempMatches: Match[] = [];
  let matchId = 0;

  shuffledMovies.map((movie, index) => {
    if (index % 2 === 0) {
      tempMatches[matchId] = { contenders: [movie] };
    } else {
      tempMatches[matchId].contenders.push(movie);
      matchId++;
    }
  });

  matches.value = tempMatches;
  currentMatch.value = tempMatches[currentMatchIndex.value];
});
</script>
