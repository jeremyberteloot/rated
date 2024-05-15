# Rated

Rated is a small demo app that I made to dust off my Vue.js skills.
The app lets you vote for the best movie of all time in a 16 contenders tournament.

## Technical details

- I wanted to train myself on the Vue composition API with Typescript.
- I chose Nuxt mainly for quality of life and for SSG (the app is static and hosted on Vercel).
- I chose Tailwind to quickly bootstrap styled but found myself a bit restricted by the library and ended up writing additional CSS to give the app it's "gamey" feel.
- I used [TMDB](https://www.themoviedb.org/) for data, I initially wanted to pull random movies from their API but feared I would reach the API cap, so I just pulled the "Top 20 movies" once, and put it in a `mock.json` file instead.

## What I would improve with more time

- Make the game replayable and more interesting by pulling random movies from the TMDB API instead of using the mock file.
- Unify the app styles, either using solely Tailwind or writing all styles myself, but having both is kind of weird.
