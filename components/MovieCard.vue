<template>
  <div class="card">
    <div class="card__image">
      <img :src="movie.poster" alt="Movie poster" />
      <p class="card__details-rating">{{ movie.imdbRating }}/10</p>
    </div>
    <div class="card__details">
      <h2 class="card__details-title">
        <v-clamp autoresize :max-lines="2">
          {{ movie.title }}
        </v-clamp>
      </h2>
      <div class="card__details-subtitle">
        <p>
          <v-clamp autoresize :max-lines="1">
            {{ movie.genre }}
          </v-clamp>
        </p>
        <p>
          <v-clamp autoresize :max-lines="1">
            {{ movie.country }}
          </v-clamp>
        </p>
        <p>{{ movie.year }}</p>
      </div>
    </div>
    <DeleteButton class="card__button" @delete="handleClick" />
  </div>
</template>

<script>
import VClamp from 'vue-clamp';

export default {
  name: 'MovieCard',
  components: { VClamp },

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
@import '~/styles/_variables.scss';

// * {
//   outline: solid 0.1rem red;
// }

.card {
  display: flex;
  gap: 16px;
  height: 150px;

  @include lg {
    height: 256px;
  }

  &__image {
    flex: 0 0 30%;
    position: relative;
    bottom: 16px;
    left: 48px;

    @include lg {
      flex: 0 0 20%;
    }

    img {
      width: 100%;
      height: 90%;
      object-fit: cover;
      object-position: 100% 0;
      border-radius: 10px;
    }
  }

  &__details {
    flex: 0 1 100%;
    border-radius: 10px;
    padding: 16px 16px 16px 48px;
    display: flex;
    flex-direction: column;
    justify-content: space-between;

    @include lg {
      padding: 32px 32px 32px 64px;
    }
  }

  &__details-title {
    flex-grow: 1;
  }

  &__details-rating {
    font-size: 12px;
    position: relative;
    left: 16px;
    bottom: -3px;

    &:before {
      content: '⭐';
    }

    @include lg {
      font-size: 14px;
      bottom: -5px;
    }
  }

  &__details-subtitle {
    width: 80%;
  }

  &__button {
    position: relative;
    right: 48px;
    bottom: 16px;
    height: 24%;
    align-self: flex-end;

    @include lg {
      right: 64px;
    }
  }
}

.card__image img,
.card__details {
  box-shadow: rgba(50, 50, 93, 0.25) 0px 13px 27px -5px,
    rgba(0, 0, 0, 0.3) 0px 8px 16px -8px;
}
</style>
