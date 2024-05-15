<template>
  <div class="w-full h-full flex flex-col items-center justify-between">
    <header class="flex w-full px-8 py-4">
      <img src="/rated-logo.svg" alt="Rated logo" class="w-32" />
    </header>
    <div class="flex items-center justify-center">
      <WinningMovie v-if="winner && currentStage === 0" :winner="winner" />

      <div class="flex flex-col items-center gap-4" v-else>
        <h1 class="round" :class="currentStage === 1 && 'finals'">
          {{ gameStageLookup[currentStage] }}
        </h1>

        <ul
          v-if="currentMatch?.contenders"
          class="flex items-center gap-4 cards-wrapper"
        >
          <MovieCard
            side="left"
            :movie="currentMatch.contenders[0]"
            @qualify="qualify"
          />
          <div class="versus">VS</div>
          <MovieCard
            side="right"
            :movie="currentMatch.contenders[1]"
            @qualify="qualify"
          />
        </ul>
      </div>
    </div>
    <footer
      class="text-xs w-full flex items-center justify-center bg-slate-700 text-white flex gap-2 p-4 self-end"
    >
      <img src="/tmdb.svg" alt="TMDB logo" class="w-16" />
      This product uses the TMDB API but is not endorsed or certified by TMDB.
    </footer>
  </div>
</template>

<style>
@import url("~/assets/app.css");
@import url("https://fonts.googleapis.com/css2?family=Luckiest+Guy&display=swap");

:root {
  font-family: "Luckiest Guy", cursive;
}
.round {
  font-size: 1.4rem;
  color: #f9f7f7;
}
.round.finals {
  font-size: 2rem;
}
@media (max-width: 1164px) {
  .cards-wrapper {
    flex-direction: column;
  }
}
.versus {
  font-size: 3rem;
  color: #ff204e;
}
footer {
  font-family: system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", Roboto,
    Oxygen, Ubuntu, Cantarell, "Open Sans", "Helvetica Neue", sans-serif;
}
</style>

<script setup lang="ts">
import { ref, onMounted } from "vue";
import type { Ref } from "vue";
import type { Movie, Match } from "~/types";
import mock from "~/public/mock.json";

const matches: Ref<Match[]> = ref([]);
const currentMatchIndex = ref(0);
const currentMatch: Ref<Match | null> = ref(null);
const currentStage: Ref<number> = ref(8);
const currentStageQualifiers: Ref<Movie[]> = ref([]);
const winner: Ref<Movie | null> = ref(null);

const gameStageLookup: { [key: number]: string } = {
  8: "8th finals",
  4: "Quarter finals",
  2: "Semi-finals",
  1: "FINALS",
};

function endGame() {
  currentStage.value = 0;
}

/**
 * Qualifies the winner movie to the next round, if the player voted for the
 * last match in a round, advance to the next round and prepare the list of contenders.
 * @param movie The movie that just won the match
 */
function qualify(movie: Movie) {
  if (currentStage.value === 1) {
    winner.value = movie;
    endGame();
  }

  currentStageQualifiers.value.push(movie);
  currentMatchIndex.value++;

  if (currentMatchIndex.value >= currentStage.value) {
    currentStage.value = currentStage.value / 2;
    prepareNextRound(currentStageQualifiers.value);
  }

  currentMatch.value = matches.value[currentMatchIndex.value];
}

/**
 * Transforms a list of movies into matches, putting them in random pairs.
 * @param movies The movies that reach the next round
 */
function prepareNextRound(movies: Movie[]) {
  const tempMatches: Match[] = [];
  let matchId = 0;

  movies.map((movie, index) => {
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

onMounted(async () => {
  const shuffledMovies: Movie[] = shuffle(mock.results.slice(0, 16));
  prepareNextRound(shuffledMovies);
  currentMatch.value = matches.value[currentMatchIndex.value];
});
</script>
