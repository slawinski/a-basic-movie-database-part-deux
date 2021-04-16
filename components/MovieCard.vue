<template>
  <div class="card">
    <div class="card__image">
      <img :src="movie.poster" alt="Movie poster" />
    </div>
    <div class="card__details">
      <h2 class="card__details-title">{{ movie.title }} ({{ movie.year }})</h2>
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
@import './styles/_variables';

.card {
  display: flex;
  gap: 8px;
  height: 150px;

  @include lg {
    height: 256px;
  }

  &__image {
    flex: 0 0 30%;

    @include lg {
      flex: 0 0 20%;
    }

    img {
      width: 100%;
      height: 100%;
      object-fit: cover;
      object-position: 100% 0;
      border-radius: 15px;
    }
  }
}
</style>
