<template>
  <div>
    <nuxt class="app" />
  </div>
</template>

<style scoped lang="scss">
@import '~/styles/_variables.scss';

.app {
  margin: 0 8px;

  @include lg {
    margin: auto;
    width: 900px;
  }
}
</style>
