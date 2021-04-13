<template>
  <div>
    <div>
      <SearchBar @input="submit" />
      <div>
        <div v-for="movie in movies" :key="movie.id" class="m-4">
          <MovieCard :movie="movie" />
        </div>
      </div>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import gql from 'graphql-tag';

export default {
  name: 'Home',
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
    async submit(movie) {
      let result = null;
      try {
        result = await axios.get(
          `https://www.omdbapi.com/?apikey=869369bc&t=${movie}`,
        );
      } catch (err) {
        console.error(err);
      } finally {
        movie = null;
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
