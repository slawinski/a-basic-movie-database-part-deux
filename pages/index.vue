<template>
  <div>
    <SearchBar @input="submit" />
    <div class="app__list">
      <template v-for="movie in movies">
        <MovieCard :key="movie.id" :movie="movie" @remove="remove" />
      </template>
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
            imdbRating
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
              imdbRating
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
                imdbRating
              }
            }
          `,
          variables: {
            movie: {
              title: this.fetchedMovie.data.Title,
              year: this.fetchedMovie.data.Year,
              poster: this.fetchedMovie.data.Poster,
              plot: this.fetchedMovie.data.Plot,
              imdbRating: this.fetchedMovie.data.imdbRating,
            },
          },
        });
      }
    },
  },
};
</script>

<style lang="scss" scoped>
.app {
  &__list {
    padding: 32px 0;
    margin-left: -48px;

    > :nth-last-child(n + 2) {
      margin-bottom: 32px;
    }
  }
}
</style>
