<template>
  <div>
    <div>
      <h1 class="text-2xl">
        a-basic-movie-database-part-deux
      </h1>
      <p class="font-bold">movie list:</p>
      <div v-for="movie in movies" :key="movie.id">
        {{ movie.title }}
        <span @click.prevent="remove(movie)" @keydown="remove(movie)">❌</span>
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
          class="ml-4 py-1 px-2 bg-transparent hover:bg-blue-500 text-button text-xl font-sans font-bold hover:text-white border border-grey-900 hover:border-transparent rounded"
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
    movies: {
      query: gql`
        query getMovies {
          movies {
            id
            title
          }
        }
      `,
      update(data) {
        return data.movies;
      },
      subscribeToMore: {
        document: gql`
          subscription mySubscription {
            movies {
              id
              title
            }
          }
        `,
        updateQuery: (previousResult, { subscriptionData }) => {
          return subscriptionData.data;
        },
      },
    },
  },
  data() {
    return {
      lookupMovie: null,
      fetchedMovie: null,
      movies: null,
    };
  },
  methods: {
    remove(movie) {
      this.$apollo.mutate({
        mutation: gql`
          mutation MyQuery($id: uuid!) {
            delete_movies(where: { id: { _eq: $id } }) {
              affected_rows
            }
          }
        `,
        variables: {
          id: movie.id,
        },
      });
    },
    async submit() {
      let result = null;
      try {
        result = await axios.get(
          `https://www.omdbapi.com/?apikey=869369bc&t=${this.lookupMovie}`,
        );
      } catch (err) {
        console.error(err);
      } finally {
        this.lookupMovie = null;
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
