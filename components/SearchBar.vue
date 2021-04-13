<template>
  <div>
    <form>
      <input
        v-model="lookupMovie"
        type="text"
        placeholder="Enter movie title"
        aria-label="Movie title"
        required
      />
      <button type="submit" @click.prevent="handleClick" @keydown="handleClick">
        Submit
      </button>
    </form>
  </div>
</template>

<script>
export default {
  name: 'SearchBar',
  data() {
    return {
      lookupMovie: null,
    };
  },
  methods: {
    handleClick(e) {
      this.$emit('input', this.lookupMovie);
    },
  },
};
</script>

<style lang="scss" scoped></style>
