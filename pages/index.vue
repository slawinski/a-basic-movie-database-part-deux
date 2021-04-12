<template>
  <div>
    <div>
      <h1>a-basic-movie-database-part-deux</h1>
      <form>
        <input
          v-model="lookupMovie"
          type="text"
          placeholder="Enter movie title"
          aria-label="Movie title"
          required
        />
        <button type="submit" @click.prevent="submit" @keydown="submit">
          Submit
        </button>
      </form>
      <div>
        <div v-for="movie in movies" :key="movie.id" class="m-4">
          <div>
            <div>
              <img
                class="w-full object-cover"
                :src="movie.poster"
                alt="Movie poster"
              />
            </div>
            <div>
              <div>{{ movie.title }} ({{ movie.year }})</div>
              <p>
                {{ movie.plot }}
              </p>
              <button @click.prevent="remove(movie)" @keydown="remove(movie)">
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
