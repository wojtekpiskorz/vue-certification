<script setup>
import { ref, computed } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";
const movies = ref(items);
function updateRating(movieIndex, rating) {
  movies.value[movieIndex].rating = rating;
}

// - The rating of each movie should starts with a `null` value.
// - The computed property named `computedMovies` returns the `movies`, adding a `notRated` value to each movie if it was rated or not.
// - Based on the `notRated` value property the text color of the `#rating` element changes from `text-yellow-500` if `true`, to`text-gray-500` if `false`.
// - The `#rating-stars` element only shows if the movie was rated. If not, it should be hidden.

const computedMovies = computed(() => {
  return movies.value.map((movie) => {
    return {
      ...movie,
      notRated: movie.rating === null,
    };
  });
});



</script>

<template>
  <div class="app">
    <div class="movie-list">
      <div
        class="movie-item relative"
        v-for="(movie, movieIndex) in computedMovies"
        :key="movie.id"
      >
        <div class="movie-item-image-wrapper">
          <img
            :src="movie.image"
            class="movie-item-image"
            alt=""
          />
        </div>

        <div class="movie-item-content-wrapper">
          <div class="movie-item-title-wrapper">
            <h3 class="movie-item-title">{{ movie.name }}</h3>
            <div class="movie-item-genres-wrapper">
              <span
                v-for="genre in movie.genres"
                :key="`${movie.id}-${genre}`"
                class="movie-item-genre-tag"
              >{{ genre }}</span>
            </div>
          </div>
          <div class="movie-item-description-wrapper">
            <p class="movie-item-description">{{ movie.description }}</p>
          </div>
          <div class="movie-item-rating-wrapper">
            <span class="movie-item-rating-text">
              Rating: ({{ movie.rating }}/5)
            </span>

            <div class="movie-item-star-icon-wrapper">
              <button
                v-for="star in 5"
                :key="star"
                class="movie-item-star-icon-button"
                :class="[
                    star <= movie.rating ? 'text-yellow-500' : 'text-gray-500',
                  ]"
                :disabled="star === movie.rating"
                @click="updateRating(movieIndex, star)"
              >
                <StarIcon class="movie-item-star-icon" />
              </button>
            </div>
          </div>
        </div>
        <div class="movie-item-floating-rating absolute right-4 top-4">
          <div class="relative">
            <StarIcon class="h-16 w-16 text-amber-600" />
            <span
              class="absolute top-1/2 left-1/2 transform -translate-x-1/2 -translate-y-1/2 text-xl font-bold"
              :class="[
                  movie.notRated ? 'text-gray-500' : 'text-yellow-500',
                ]"
            > {{ movie.rating !== null ? movie.rating : '-' }}</span>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
