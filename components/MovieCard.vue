<template>
  <div>
    <div>
      <img :src="movie.poster" alt="Movie poster" />
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
</template>

<script>
export default {
  name: 'MovieCard',
  props: {
    movie: {
      type: Object,
      default: () => {},
    },
  },
};
</script>

<style lang="scss" scoped></style>
