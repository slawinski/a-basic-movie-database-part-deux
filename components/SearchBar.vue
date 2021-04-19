<template>
  <form class="container" @submit.prevent="handleSubmit">
    <input
      v-model="lookupMovie"
      type="text"
      placeholder="Enter movie title"
      aria-label="Movie title"
      required
    />
    <button type="reset" class="search"></button>
  </form>
</template>

<script>
export default {
  name: 'SearchBar',
  emits: ['input'],
  data() {
    return {
      lookupMovie: null,
    };
  },
  methods: {
    handleSubmit() {
      this.$emit('input', this.lookupMovie);
      this.lookupMovie = null;
    },
  },
};
</script>

<style lang="scss" scoped>
@import '~/styles/_variables.scss';

* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

html,
body {
  width: 100%;
  height: 100%;
}

body {
  background: #252525;
}

button {
  border: 0;
}

.container {
  position: relative;
  margin: auto;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  width: 300px;
  height: 100px;
  .search {
    position: absolute;
    margin: auto;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    width: 50px;
    height: 50px;
    background: #ff5f1f;
    border-radius: 10px;
    transition: all 1s, width 1ms, height 1ms;
    z-index: 4;
    box-shadow: 0 2px 25px rgba(255, 115, 0, 0.5);

    @include lg {
      width: 70px;
      height: 70px;
    }
    // box-shadow: 0 0 25px 0  #FF5F1F;
    &:hover {
      cursor: pointer;
    }
    &::before {
      content: '';
      position: absolute;
      margin: auto;
      top: 22px;
      right: 0;
      bottom: 0;
      left: 22px;
      width: 12px;
      height: 3px;
      background: white;
      transform: rotate(45deg);
      transition: all 0.5s;
    }
    &::after {
      content: '';
      position: absolute;
      margin: auto;
      top: -5px;
      right: 0;
      bottom: 0;
      left: -5px;
      width: 25px;
      height: 25px;
      border-radius: 50%;
      border: 3px solid white;
      transition: all 0.5s;
    }
  }
  input {
    font-family: 'Poppins', monospace;
    position: absolute;
    margin: auto;
    top: 0;
    right: 0;
    bottom: 0;
    left: 0;
    width: 50px;
    height: 50px;
    outline: none;
    border: none;
    // border-bottom: 1px solid rgba(255, 255, 255, 0.2);
    background: #ff5f1f;
    color: white;
    text-shadow: 0 2px 25px rgba(255, 115, 0, 0.5);
    padding: 0 0 0 20px;
    border-radius: 10px;
    box-shadow: 0 2px 25px rgba(255, 115, 0, 0.5);
    // box-shadow: inset 0 0 25px 0 rgba(0, 0, 0, 0.5);
    transition: all 1s;
    opacity: 0;
    z-index: 5;
    font-weight: normal;
    letter-spacing: 0.1em;

    @include lg {
      width: 70px;
      height: 70px;
    }

    &:hover {
      cursor: pointer;
    }
    &:focus {
      width: 300px;
      opacity: 1;
      padding: 0 80px 0 20px;
      cursor: text;
    }
    &:focus ~ .search {
      right: -250px;
      background: #ff5f1f;
      z-index: 6;
      &::before {
        top: 0;
        left: 0;
        width: 25px;
      }
      &::after {
        top: 0;
        left: 0;
        width: 25px;
        height: 3px;
        border: none;
        background: white;
        border-radius: 0%;
        transform: rotate(-45deg);
      }
    }
    &::placeholder {
      color: white;
      opacity: 0.5;
      font-weight: normal;
    }
  }
}
</style>
