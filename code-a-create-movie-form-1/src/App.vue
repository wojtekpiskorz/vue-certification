<script setup>
import { ref, reactive, watch, computed } from "vue";
import { StarIcon } from "@heroicons/vue/24/solid";
import { items } from "./movies.json";
const movies = ref(items);


const moviesAmount = computed(() => movies.value.length);

// Set rating functionality

function updateRating(movieIndex, rating) {
  movies.value[movieIndex].rating = rating;
}

function resetRating() {
  movies.value.forEach((movie) => (movie.rating = 0));
}

const averageRating = computed(() => {
  const totalRating = movies.value.reduce(
    (total, movie) => total + movie.rating,
    0
  );
  return (totalRating / movies.value.length).toFixed(1);
});

// form 

const showForm = ref(false);

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

// Form validation and errors handling

const errors = reactive({
  name: null,
  genres: null,
  description: null,
  image: null,
  inTheaters: null,
});

const formIsValid = ref(false);

function validateForm() {
  errors.name = !movieForm.name ? "Name is required" : null;
  errors.genres = !movieForm.genres.length ? "Genres is required" : null;
  formIsValid.value = !Object.values(errors).some((error) => error);
}

// watch([movieForm.name, movieForm.description, movieForm.image], () => {
//   if (movieForm.name) errors.name = null;
//   if (movieForm.description) errors.description = null;
//   if (movieForm.image) errors.image = null;
// });

// watch(movieForm.genres, () => {
//   if (movieForm.genres.length) errors.genres = null;
// });

// watch(movieForm.inTheaters, () => {
//   if (movieForm.inTheaters) errors.inTheaters = null;
// });


// Form actions

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
  isMovieEditing.value = false;
}

function cancelForm() {
  clearForm();
}

function submitForm() {
  validateForm();
  if (!formIsValid.value) return
  if (isMovieEditing.value) {
    updateMovie();
  } else {
    addMovie();
  }
  clearForm();
}

// add movie

function addMovie() {
  if (!formIsValid) return
  const newMovie = {
    id: Number(new Date()) + movieForm.name,
    ...movieForm,
  };
  movies.value.push(newMovie);
}


// edit movie

const isMovieEditing = ref(false);

function editMovie(movie) {
  isMovieEditing.value = true;
  movieForm.name = movie.name;
  movieForm.description = movie.description;
  movieForm.image = movie.image;
  movieForm.genres = movie.genres;
  movieForm.inTheaters = movie.inTheaters;
  movieForm.id = movie.id;
  showForm.value = true;
  console.log(movieForm);
}

function updateMovie() {
  if (!formIsValid.value) return

  const movie = movies.value.find(
    (movie) => movie.id === movieForm.id
  );
  const updatedMovie = {
    ...movie,
    ...movieForm,
  };

  movie.name = updatedMovie.name;
  movie.description = updatedMovie.description;
  movie.image = updatedMovie.image;
  movie.genres = updatedMovie.genres;
  movie.inTheaters = updatedMovie.inTheaters;
  if (!movie.id) movie.id = Number(new Date()) + movie.name;

}

// delete movie

function deleteMovie(movie) {
  const selectedMovie = movies.value.findIndex(
    (movieItem) => movieItem.id === movie.id
  );
  movies.value.splice(selectedMovie, 1);
}




</script>

<template>
  <div class="app flex flex-col gap-12">
    <!-- Header  -->
    <header class=" text-white py-4 px-8 flex justify-between items-center max-w-4xl w-full">
      <h1 class="text-2xl font-bold">Movies</h1>
      <div class="flex gap-8 items-center">
        <Button
          class="border border-white border-solid py-3 px-6 rounded-md hover:bg-white hover:text-black"
          @click="resetRating"
        >Reset Rating
        </Button>
        <span class="">Average Rating: <strong>{{ averageRating }}</strong></span>
        <span class="">Number or movies: <strong>{{ moviesAmount }}</strong></span>
      </div>
    </header>
    <!-- Add a movie button -->
    <div
      @click="showForm = true"
      class="fixed flex items-center justify-center bottom-4 right-4 w-16 h-16 rounded-full bg-sky-600 text-white font-bold text-4xl line-height-0 cursor-pointer"
    >
      +
    </div>
    <!-- Form  -->
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
    <!-- Movie list -->
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
            <!-- buttons  -->
            <div class="flex gap-2 flex-wrap">
              <!-- edit button -->
              <button
                class="movie-item-edit-button px-2 hover:bg-gray-200  border border-gray-500 border-solid rounded-lg"
                @click="editMovie(movie)"
              >
                Edit
              </button>
              <!-- delete button -->
              <button
                class="movie-item-edit-button px-2 hover:bg-gray-200 border border-gray-500 border-solid rounded-lg"
                @click="deleteMovie(movie)"
              >
                Delete
              </button>
            </div>
          </div>
        </div>
      </div>
    </div>
  </div>
</template>
