<script setup>
/*
I have a new idea to download a huge database of movies and query it to find movies or to answer questions such as: what was the best action movie of the year 2015, or which year or years have the highest average score?

Can you help me build it?

Note: please do not update/change the file movies.json for this exercise!

level 1: Let's have a list showing the movies in the movies.json file. Show all relevant information such as title, genre, score, etc. However, I'm collecting a lot of movies, so please paginate the list to show X movies at a time only, and add an input field where I can set this value of X. Of course, the list should update to the correct length at all times.

level 2: Add first page, previous page, next page and last page buttons to the list. Make sure they are only active when relevant! Also, I would like to know the current page I'm looking at, and the total number of pages available.

level 3: Add an input search field for the title of the movie. The list should update to show only movies with titles that match my search query. Please don't make it too restrictive, I would like for example that if I type 'wars', all 'Star Wars' movies should be matched.

level 4: Add a multiselector for one or more years. The options should be all different years for which I have collected movies. Of course, the list should update to show only movies released in any of the chosen years.

level 5: Add a multiselector for one or more genres. The options should be all different genres existing in the list of movies. Since I collected the movies from different sources, some genres are duplicated and don't really match, I expect you to fix this issue to show consistent options. Of course, the list should update to show only movies of any of the chosen genres.

level 5: I'd like to know at all times what is the average score of ALL the movies that are listed (not considering the pagination), so please show it at the top of the list.


bonus level: Apply nice styling to this UI!
*/

import movies from "./assets/movies.json";
import { computed, ref, watch } from "vue";
import MovieCard from "./components/MovieCard.vue";
import Pagination from "./components/Pagination.vue";
import Search from "./components/Search.vue";
import BlankResult from "./components/BlankResult.vue";
import MultipleSelection from "./components/MultipleSelection.vue";

const genreMapping = {
  "Sci-fi": ["sci-fi", "Science Fiction"],
  Action: ["action"],
  Comedy: ["comedy"],
  Xmas: ["xmas"],
  Terror: ["terror"],
  Animation: ["animation"],
};
const currentPage = ref(1);
const numberOfItemPerPage = ref(6);
const allMovies = sortingMovieByScore(transformGenre());
const currentMovies = ref(allMovies);
const searchResult = ref("");
const filteredMovies = ref([]);
const selectedDates = ref([]);
const selectedGenres = ref([]);

// init Pagination
paginate(currentMovies, currentPage.value);

watch(currentPage, (newPage) => {
  if (isFiltering()) {
    paginate(filteredMovies, newPage);
  } else {
    paginate(ref(allMovies), newPage);
  }
});

watch(filteredMovies, (newMovies) => {
  paginate(isFiltering() ? ref(newMovies) : ref(allMovies), 1);
});

function transformGenre() {
  let transformData = movies.map((movie) => {
    for (let [key, genreList] of Object.entries(genreMapping)) {
      genreList = genreList.map((genre) => genre.toLowerCase());
      if (genreList.includes(movie.genre.toLowerCase())) {
        return {
          ...movie,
          genre: key,
          score: Number(movie.score),
        };
      }
    }
  });
  return transformData;
}

function paginate(movies, currentPage) {
  const current = currentPage;
  const itemPerPage = numberOfItemPerPage.value;
  const from = current * itemPerPage - itemPerPage;
  const to = current * itemPerPage;
  currentMovies.value = movies.value.slice(from, to);
}

function sortingMovieByScore(movies) {
  return movies.sort((movie1, movie2) => {
    return movie2.score - movie1.score;
  });
}

function updatePagination(value) {
  currentPage.value = value;
}

function searchMovie(value) {
  searchResult.value = value;
  filteringMovies();
}

function getSelectedDates(value) {
  selectedDates.value = value;
  filteringMovies();
}

function getSelectedGenres(value) {
  selectedGenres.value = value;
  filteringMovies();
}

function setItemPerPage(value) {
  if (value > 0) {
    numberOfItemPerPage.value = value;
    paginate(
      isFiltering() ? filteredMovies : ref(allMovies),
      currentPage.value
    );
  }
}

function filteringMovies() {
  let tempMovies = allMovies;
  if (isFiltering()) {
    const filter = tempMovies.filter((movie) => {
      if (searchResult.value != "") {
        if (
          movie.title.toLowerCase().indexOf(searchResult.value.toLowerCase()) !=
          -1
        ) {
          if (selectedDates.value.length > 0) {
            if (selectedDates.value.includes(movie.year)) {
              if (selectedGenres.value.length > 0) {
                if (selectedGenres.value.includes(movie.genre)) {
                  return movie; // name, genre and year are matching
                } else {
                  return null;
                }
              }
              return movie; // only name and year are matching
            }
          } else if (selectedGenres.value.length > 0) {
            if (selectedGenres.value.includes(movie.genre)) {
              return movie; // only name and genre are matching
            }
          } else {
            return movie; // only name
          }
        }
      } else if (
        selectedGenres.value.includes(movie.genre) &&
        selectedDates.value.includes(movie.year)
      ) {
        return movie; // genre and year are matching
      } else if (
        selectedDates.value.length == 0 &&
        selectedGenres.value.includes(movie.genre)
      ) {
        return movie; // only genre
      } else if (
        selectedGenres.value.length == 0 &&
        selectedDates.value.includes(movie.year)
      ) {
        return movie; // only year
      }
    });
    filteredMovies.value = sortingMovieByScore(filter);
  } else {
    filteredMovies.value = [];
  }
  currentPage.value = 1;
}

function getAllYears() {
  const years = allMovies.map((movie) => movie.year);
  return [...new Set(years)].sort().reverse();
}

function getAllGenres() {
  const genres = allMovies.map((movie) => movie.genre);
  return [...new Set(genres)].sort();
}

function isFiltering() {
  return (
    searchResult.value != "" ||
    selectedDates.value.length > 0 ||
    selectedGenres.value.length > 0
  );
}

const totalPage = computed(() => {
  if (filteredMovies.value.length > 0 || isFiltering()) {
    return (
      Math.ceil(filteredMovies.value.length / numberOfItemPerPage.value) || 1
    );
  } else {
    return Math.ceil(allMovies.length / numberOfItemPerPage.value);
  }
});

const averageScoreForFilteredMovies = computed(() => {
  return averageScore(
    filteredMovies.value.length > 0 ? filteredMovies.value : allMovies
  ).toFixed(2);
});

const averageScoreForCurrentShowingMovies = computed(() => {
  return averageScore(currentMovies.value).toFixed(2);
});

const averageScoreForAllMovies = computed(() => {
  return averageScore(allMovies).toFixed(2);
});

function averageScore(list) {
  const sum = list
    .map((movie) => movie.score)
    .reduce((prev, current) => prev + current, 0);
  return sum / list.length || 0;
}
</script>

<template>
  <link rel="stylesheet" href="https://maxcdn.bootstrapcdn.com/font-awesome/4.4.0/css/font-awesome.min.css" />

  <body>
    <h3>Movies</h3>
    <div class="flex-col center">
      <h4 class="flex center">Search Movie</h4>
      <Search @onSearchButtonClicked="searchMovie($event)"></Search>
    </div>
    <div class="flex-col center">
      <h4 class="flex center">Item per Page</h4>
      <Search :isNumberOnly="true" :hideButton="true" :placeholder="'Item per page..'"
        @onSearchButtonClicked="setItemPerPage($event)"></Search>
    </div>
    <div class="flex-row content-center gap">
      <div class="flex-col content-center">
        <h4 class="flex center">Average Score</h4>
        <div class="flex-col center average-container">
          <p class="average-score mtb-5">
            All Movies: {{ averageScoreForAllMovies }}
          </p>
          <p class="average-score mtb-5">
            Current Page: {{ averageScoreForCurrentShowingMovies }}
          </p>
          <p class="average-score mtb-5">
            All Filtered Movies: {{ averageScoreForFilteredMovies }}
          </p>
        </div>
      </div>
      <div class="flex-col center mrg-40">
        <h4>Filtered By</h4>
        <div class="flex-row gap center">
          <div class="flex-col center">
            <p class="header">Year</p>
            <MultipleSelection :lists="getAllYears()" :currentSelectedItems="selectedDates"
              @updateSelectedItems="getSelectedDates($event)"></MultipleSelection>
          </div>
          <div class="flex-col center">
            <p class="header">Genre</p>
            <MultipleSelection :lists="getAllGenres()" :currentSelectedItems="selectedGenres"
              @updateSelectedItems="getSelectedGenres($event)"></MultipleSelection>
          </div>
        </div>
      </div>
    </div>

    <div class="grid" v-if="currentMovies.length > 0">
      <div v-for="item in currentMovies" v-bind:key="item.id">
        <MovieCard :movie="item"></MovieCard>
      </div>
    </div>
    <div v-else>
      <BlankResult></BlankResult>
    </div>
    <Pagination :currentPage="currentPage" :totalPage="totalPage" @forwardToPrevPage="updatePagination($event)"
      @forwardToNextPage="updatePagination($event)" @forwardToFirstPage="updatePagination($event)"
      @forwardToLastPage="updatePagination($event)">
    </Pagination>
  </body>
</template>

<style>
@import url("https://fonts.googleapis.com/css2?family=Lato:ital,wght@0,100;0,300;0,400;0,700;0,900;1,100;1,300;1,400;1,700;1,900&display=swap");

body {
  background: black;
  color: white;
  font-family: "Lato", sans-serif;
  margin: 30px;
}

.fa {
  color: #ff2c1f;
}

.flex-end {
  display: flex;
  align-items: end;
  justify-content: end;
}

h3 {
  font-size: 50px;
  font-weight: 700;
  display: flex;
  justify-content: center;
  margin: 40px 0;
  text-transform: capitalize;
  font-style: italic;
}

h4 {
  font-size: 35px;
  font-weight: 700;
  font-style: italic;
  margin-bottom: 15px;
  margin-top: 0;
  width: 100%;
  text-align: center;
}

.grid {
  display: grid;
  grid-gap: 40px;
  margin: 20px;
  grid-template-columns: repeat(auto-fill, minmax(220px, 1fr));
  justify-content: center;
  width: 70%;
  margin: auto;
}

.flex {
  display: flex;
}

.flex-col {
  display: flex;
  flex-direction: column;
}

.flex-row {
  display: flex;
  flex-direction: row;
  flex-wrap: wrap;
}

.center {
  align-items: center;
  justify-content: center;
}

p {
  font-size: 12px;
  font-weight: 300;
  margin: 0;
}

.space-between {
  justify-content: space-between;
  align-items: start;
}

.average-score {
  font-size: 24px;
}

.gap {
  gap: 30px;
}

.mrg-40 {
  margin-bottom: 40px;
}

.header {
  font-size: 20px;
  font-weight: 500;
  margin: 10px;
}

.mtb-5 {
  margin: 5px 0;
}

.content-center {
  justify-content: center;
  align-items: flex-start;
}

.average-container {
  margin: 50px 0;
}
</style>
