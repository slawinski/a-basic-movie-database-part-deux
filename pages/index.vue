<template>
  <div>
    <SearchBar @input="submit" />
    <div class="app__list">
      <template v-for="movie in movies.slice().reverse()">
        <MovieCard :key="movie.id" :movie="movie" @remove="remove" />
      </template>
    </div>
  </div>
</template>

<script>
import axios from 'axios';
import getMovies from '@/apollo/getMovies.gql';
import addMovie from '@/apollo/addMovie.gql';
import deleteMovie from '@/apollo/deleteMovie.gql';
import mySubscription from '@/apollo/mySubscription.gql';

export default {
  name: 'Home',
  apollo: {
    movies: {
      query: getMovies,
      update(data) {
        return data.movies;
      },
      subscribeToMore: {
        document: mySubscription,
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
        mutation: deleteMovie,
        variables: {
          id: movie.id,
        },
      });
    },
    async submit(movie) {
      try {
        this.fetchedMovie = await axios.get(
          `https://www.omdbapi.com/?apikey=869369bc&t=${movie}`,
        );
        await this.$apollo.mutate({
          mutation: addMovie,
          variables: {
            movie: {
              title: this.fetchedMovie.data.Title,
              year: this.fetchedMovie.data.Year,
              poster: this.fetchedMovie.data.Poster,
              plot: this.fetchedMovie.data.Plot,
              imdbRating: this.fetchedMovie.data.imdbRating,
              genre: this.fetchedMovie.data.Genre,
              runtime: this.fetchedMovie.data.Runtime,
              country: this.fetchedMovie.data.Country,
            },
          },
        });
      } catch (err) {
        console.log(err);
      }
    },
  },
};
</script>

<style lang="scss" scoped>
@import '~/styles/_variables.scss';

.app {
  &__list {
    padding: 32px 0;
    margin-left: -48px;
    margin-right: -48px;

    @include lg {
      margin-right: -64px;
      padding: 64px 0;
    }

    > :nth-last-child(n + 2) {
      margin-bottom: 32px;

      @include sm {
        margin-bottom: 48px;
      }

      @include lg {
        margin-bottom: 56px;
      }
    }
  }
}
</style>
