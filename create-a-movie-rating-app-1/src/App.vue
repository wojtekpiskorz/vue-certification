<script setup>
import { items } from "./movies.json";
import { StarIcon } from "@heroicons/vue/24/solid";
import { reactive } from "vue";

const movies = reactive(items);


// - There are 5 buttons from 1 to 5 representing the rating on for each movie.
// - Clicking on a button updates the rating of that movie.
// - The button for the rating number is disabled if that rating is the current one of the movie.


const setRatingTo = (rating, movie) => {
  movie.rating = rating;
};

</script>

<template>
  <!-- This is where your template goes	-->
  <ul class="max-w-4xl mx-auto grid xl:grid-cols-3 gap-10 mt-20 justify-items-start">
    <li
      v-for="(item, index) in movies"
      :key="item.id"
      class="card text-gray-800 flex flex-col rounded-md overflow-hidden bg-white h-auto"
      :class="`card--${index + 1}`"
    >
      <div class="card__content px-4 py-6 flex flex-col gap-4">
        <div class="card__meta flex flex-col gap-2">
          <ul
            v-if="item.genres"
            class="card__genres flex flex-wrap gap-1"
          >
            <li
              v-for="(genre, index) in item.genres"
              :key="genre + index"
              class="card__genre bg-blue-100 text-blue-600 text-xs px-2 py-1 rounded-full inline-block mr-2"
            >
              {{ genre }}
            </li>
          </ul>
          <ul
            v-if="item.rating"
            class="card__rating-wrapper flex flex-wrap gap-1"
          >
            <button
              v-for="(star, index) in 5"
              :key="star"
              @click.prevent="setRatingTo(index + 1, item)"
              :disabled="item.rating === index + 1"
              class="disabled:cursor-not-allowed "
            >
              <StarIcon
                class="card__rating-star h-5 w-5 text-gray-600"
                :class="{
                    'text-yellow-500': index < item.rating,
                  }"
              />
            </button>
          </ul>
        </div>
        <div class="card__info flex flex-col gap-2">
          <h2 class="card__title font-bold text-2xl">{{ item.name }}</h2>
          <p class="card__description text-sm text-gray-500">{{ item.description }}</p>
        </div>
      </div>
      <div class="card__image-wrapper order-first max-h-72">
        <img
          :src="item.image"
          alt=""
          class="card__image object-cover max-h-full w-full"
        />
      </div>
    </li>
  </ul>
</template>
