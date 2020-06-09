<template>
  <div>
    <div>
      <h1 class="text-2xl">
        a-basic-movie-database-part-deux
      </h1>
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
          class="bg-transparent hover:bg-blue-500 text-blue-700 font-semibold hover:text-white py-2 px-4 border border-blue-500 hover:border-transparent rounded"
          @click.prevent="submit"
          @keydown="submit"
        >
          Submit
        </button>
      </form>
      <div class="flex flex-wrap">
        <div v-for="movie in movies" :key="movie.id" class="m-4">
          <div
            class="w-64 h-full flex flex-col justify-between rounded overflow-hidden shadow-lg"
          >
            <div>
              <img
                class="w-full object-cover"
                :src="movie.poster"
                alt="Movie poster"
              />
            </div>
            <div class="px-6 py-4">
              <div class="mb-4 font-bold text-xl truncate">
                {{ movie.title }} ({{ movie.year }})
              </div>
              <p class="mb-4 h-32 text-gray-700 text-base overflow-auto">
                {{ movie.plot }}
              </p>
              <button
                class="bg-transparent hover:bg-red-500 text-red-700 font-semibold hover:text-white py-2 px-4 border border-red-500 hover:border-transparent rounded"
                @click.prevent="remove(movie)"
                @keydown="remove(movie)"
              >
                Remove
              </button>
            </div>
          </div>
        </div>
      </div>
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
            year
            poster
            plot
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
              year
              poster
              plot
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
                year
                poster
                plot
              }
            }
          `,
          variables: {
            movie: {
              title: this.fetchedMovie.data.Title,
              year: this.fetchedMovie.data.Year,
              poster: this.fetchedMovie.data.Poster,
              plot: this.fetchedMovie.data.Plot,
            },
          },
        });
      }
    },
  },
};
</script>
