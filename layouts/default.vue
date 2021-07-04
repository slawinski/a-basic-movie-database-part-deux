<template>
  <div>
    <nuxt class="app" />
  </div>
</template>

<style scoped lang="scss">
@import '~/styles/_variables.scss';

.app {
  margin: 0 8px;

  @include sm {
    margin: auto;
    width: 500px;
  }

  @include lg {
    margin: 64px auto;
    width: 900px;
  }
}
</style>
