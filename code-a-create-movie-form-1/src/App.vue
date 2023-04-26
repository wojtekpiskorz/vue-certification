<script setup>
import { ref, reactive, watch } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";
const movies = ref(items);


function updateRating(movieIndex, rating) {
  movies.value[movieIndex].rating = rating;
}

const movieForm = reactive({
  name: "",
  description: "",
  image: "",
  genres: [],
  inTheaters: false,
});

const movieGenres = reactive([
  { label: "Action", value: "action" },
  { label: "Comedy", value: "comedy" },
  { label: "Drama", value: "drama" },
  { label: "Horror", value: "horror" },
  { label: "Romance", value: "romance" },
  { label: "Thriller", value: "thriller" },
])

const errors = reactive({
  name: null,
  genres: null,
  description: null,
  image: null,
  inTheaters: null,
});

const formIsValid = ref(false);

const showForm = ref(false);

function addMovie() {
  if (!formIsValid) return
  const newMovie = {
    id: Number(new Date()) + movieForm.name,
    ...movieForm,
  };
  movies.value.push(newMovie);
}

function clearForm() {
  movieForm.name = "";
  movieForm.description = "";
  movieForm.image = "";
  movieForm.genres = [];
  movieForm.inTheaters = false;
  errors.name = null;
  errors.genres = null;
  errors.description = null;
  errors.image = null;
  errors.inTheaters = null;
  showForm.value = false;
}

function validateForm() {
  errors.name = !movieForm.name ? "Name is required" : null;
  errors.genres = !movieForm.genres.length ? "Genres is required" : null;
  formIsValid.value = !Object.values(errors).some((error) => error);
}

function cancelForm() {
  clearForm();
}

function submitForm() {
  console.log("submitForm");
  validateForm();
  console.log("errors", errors);
  console.log("formIsValid", formIsValid.value);
  if (formIsValid.value) {
    addMovie();
    clearForm();
  }
}

// watch for changes in all the inputs  and if it's not empty, set given to null
watch([movieForm.name, movieForm.description, movieForm.image], () => {
  if (movieForm.name) errors.name = null;
  if (movieForm.description) errors.description = null;
  if (movieForm.image) errors.image = null;
});

watch(movieForm.genres, () => {
  if (movieForm.genres.length) errors.genres = null;
});

watch(movieForm.inTheaters, () => {
  if (movieForm.inTheaters) errors.inTheaters = null;
});




</script>

<template>
  <div class="app">
    <div
      @click="showForm = true"
      class="fixed flex items-center justify-center bottom-4 right-4 w-16 h-16 rounded-full bg-sky-600 text-white font-bold text-4xl line-height-0 cursor-pointer"
    >
      +
    </div>
    <div
      v-show="showForm"
      class="fixed w-full z-10 h-screen flex justify-center items-center bg-gray-900 bg-opacity-40 text-white"
    >
      <div class=" max-w-lg bg-gray-100 p-12 rounded-md text-gray-800 flex flex-col">
        <form @submit.prevent="submitForm">
          <!-- text input with label name and v-model name -->
          <div class="mb-4">
            <label
              for="name"
              class="block mb-2"
            >Name</label>
            <input
              type="text"
              id="name"
              class="w-full border border-gray-300 rounded-md p-2"
              v-model="movieForm.name"
            />
            <span class="text-red-500 text-sm">{{ errors.name }}</span>
          </div>
          <!-- - The form includes a textarea for `description`. -->
          <div class="mb-4">
            <label
              for="description"
              class="block mb-2"
            >Description</label>
            <textarea
              id="description"
              class="w-full border border-gray-300 rounded-md p-2"
              v-model="movieForm.description"
            ></textarea>
            <span class="text-red-500 text-sm">{{ errors.description }}</span>
          </div>
          <!-- - The form includes an input text for `image`. -->
          <div class="mb-4">
            <label
              for="image"
              class="block mb-2"
            >Image</label>
            <input
              type="text"
              id="image"
              class="w-full border border-gray-300 rounded-md p-2"
              v-model="movieForm.image"
            />
            <span class="text-red-500 text-sm">{{ errors.image }}</span>
          </div>
          <!-- - The form includes a dropdown for `genres`, and it is required. -->
          <div class="mb-4">
            <label
              for="genres"
              class="block mb-2"
            >Genres</label>
            <select
              id="genres"
              class="w-full border border-gray-300 rounded-md p-2"
              v-model="movieForm.genres"
              multiple
            >
              <option
                v-for=" genre  in  movieGenres "
                :key="genre.value"
                :value="genre.value"
              >{{ genre.label }}</option>
            </select>
            <span class="text-red-500 text-sm">{{ errors.genres }}</span>
          </div>
          <!-- - The form includes a checkbox for `inTheaters`. -->
          <div class="mb-4 flex items-center gap-4">
            <label
              for="inTheaters"
              class="block"
            >In Theaters</label>
            <input
              type="checkbox"
              id="inTheaters"
              class="order-first border border-gray-300 rounded-md p-2"
              v-model="movieForm.inTheaters"
            />
            <span class="text-red-500 text-sm">{{ errors.inTheaters }}</span>
          </div>
          <div class="flex justify-end gap-4">
            <button
              type="button"
              class="bg-gray-300 p-2 rounded-md"
              @click.prevent="cancelForm"
            >Cancel</button>
            <button
              type="submit"
              class="bg-blue-500 p-2 rounded-md text-white"
            >Submit</button>
          </div>
        </form>
        {{ movieForm }}
      </div>
    </div>
    <div class="movie-list">
      <div
        class="movie-item"
        v-for="( movie, movieIndex ) in  movies "
        :key="movie.id"
      >
        <div class="movie-item-image-wrapper">
          <div class="movie-item-star-wrapper">
            <StarIcon
              id="rating"
              class="movie-item-star-rating-icon"
              :class="[movie.rating ? 'text-yellow-500' : 'text-gray-500']"
            />
            <div class="movie-item-star-content-wrapper">
              <span
                v-if="movie.rating"
                id="rating-stars"
                class="movie-item-star-content-rating-rated"
              >
                {{ movie.rating }}
              </span>
              <span
                v-else
                class="movie-item-star-content-rating-not-rated"
              >
                -
              </span>
            </div>
          </div>
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
                v-for=" genre  in  movie.genres "
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
                v-for=" star  in  5 "
                :key="star"
                class="movie-item-star-icon-button"
                :class="[
                    star <= movie.rating ? 'text-yellow-500' : 'text-gray-500',
                  ]
                  "
                :disabled="star === movie.rating"
                @click="updateRating(movieIndex, star)"
              >
                <StarIcon class="movie-item-star-icon" />
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
