<template>
  <div class="container">
    <div>
      <logo />
      <h1 class="title">
        a-basic-movie-database-part-deux
      </h1>
      <h2 class="subtitle">
        My outstanding Nuxt.js project
      </h2>
      <form class="links">
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
import Logo from '~/components/Logo.vue';

export default {
  components: {
    Logo,
  },
  apollo: {
    movies: gql`
      query MyQuery {
        movies {
          id
          title
          genre
          year
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
      try {
        this.fetchedMovie = await axios.get(
          `https://www.omdbapi.com/?apikey=869369bc&t=${this.lookupMovie}`,
        );
      } catch (err) {
        console.error(err);
      }
    },
  },
};
</script>

<style>
/* Sample `apply` at-rules with Tailwind CSS
.container {
  @apply min-h-screen flex justify-center items-center text-center mx-auto;
}
*/
.container {
  margin: 0 auto;
  min-height: 100vh;
  display: flex;
  justify-content: center;
  align-items: center;
  text-align: center;
}

.title {
  font-family: 'Quicksand', 'Source Sans Pro', -apple-system, BlinkMacSystemFont,
    'Segoe UI', Roboto, 'Helvetica Neue', Arial, sans-serif;
  display: block;
  font-weight: 300;
  font-size: 100px;
  color: #35495e;
  letter-spacing: 1px;
}

.subtitle {
  font-weight: 300;
  font-size: 42px;
  color: #526488;
  word-spacing: 5px;
  padding-bottom: 15px;
}

.links {
  padding-top: 15px;
}
</style>
