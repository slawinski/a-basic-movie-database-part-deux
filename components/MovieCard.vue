<template>
  <div class="card">
    <div class="card__image">
      <img :src="movie.poster" alt="Movie poster" />
    </div>
    <div class="card__details">
      <div>{{ movie.title }} ({{ movie.year }})</div>
      <p>{{ movie.imdbRating }}/10</p>
      <button @click.prevent="handleClick" @keydown="remove(movie)">
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
  emits: ['remove'],
  methods: {
    handleClick() {
      this.$emit('remove', this.movie);
    },
  },
};
</script>

<style lang="scss" scoped>
.card {
  display: flex;
  gap: 8px;

  &__image {
    img {
      width: 100%;
      height: 75%;
      object-fit: cover;
      object-position: 100% 0;
    }
  }

  &__details {
    flex: 0 0 70%;
  }
}
</style>
