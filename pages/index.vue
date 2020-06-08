<template>
  <div>
    <div>
      <h1>
        a-basic-movie-database-part-deux
      </h1>
      <p>all movies</p>
      <div v-for="movie in movies" :key="movie.id">
        {{ movie.title }}
      </div>
      <form>
        <input
          v-model="lookupMovie"
          type="text"
          class="py-1 px-3 shadow appearance-none border rounded font-body text-xl text-gray-700 leading-tight focus:outline-none focus:shadow-outline"
          placeholder="Enter movie title"
          aria-label="Movie title"
          required
        />
        <button
          type="submit"
          class="ml-4 py-1 px-2 bg-transparent hover:bg-button text-button text-xl sm:text-2xl font-sans font-bold hover:text-white border border-button hover:border-transparent rounded"
          @click.prevent="submit"
          @keydown="submit"
        >
          Submit
        </button>
      </form>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import gql from 'graphql-tag';

export default {
  apollo: {
    movies: gql`
      query getMovies {
        movies {
          id
          title
        }
      }
    `,
  },
  data() {
    return {
      lookupMovie: null,
      fetchedMovie: null,
    };
  },
  methods: {
    async submit() {
      let result = null;
      try {
        result = await axios.get(
          `https://www.omdbapi.com/?apikey=869369bc&t=${this.lookupMovie}`,
        );
      } catch (err) {
        console.error(err);
      } finally {
        this.fetchedMovie = result;
        await this.$apollo.mutate({
          mutation: gql`
            mutation addMovie($movie: movies_insert_input!) {
              insert_movies_one(object: $movie) {
                id
                title
              }
            }
          `,
          variables: {
            movie: { title: this.fetchedMovie.data.Title },
          },
        });
      }
    },
  },
};
</script>
