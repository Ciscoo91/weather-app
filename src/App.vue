<script setup lang="ts">

import {ref} from 'vue';
import Button from './components/Button.vue';
import SearchInput from './components/SearchInput.vue';
import WeeklyDropdown from './components/WeekdayDropDown.vue';
import UnitDropdowns from './components/UnitDropdowns.vue';


const movies = ref([
  "Avatar 2",
  "Lilo & Stitch",
  "Titanic",
  "Boruto Two Blue Vortex",
  "Shingeki No kyojin"
])

const deleteMovie = (movie: string) => {
  movies.value  = movies.value.filter(mv => mv !== movie)
}

const sortValues = () => {
  movies.value = movies.value.sort()
}

</script>

<template>
  <h1 class="text-4xl font-grotesque">Movies</h1>
  <ul>
    <li v-for="movie in movies" class="flex gap-4 items-center my-4" :key="movie">
      <p class="font-grotesque">{{movie}}</p>
      <Button @click="deleteMovie(movie)">Delete</Button>
    </li>
  </ul>
  <div>
    <Button @click="sortValues()">Sort Movies</Button>
  </div>
  <SearchInput />
  <WeeklyDropdown />
  <UnitDropdowns />
  <UnitDropdowns />
</template>

<style scoped></style>
