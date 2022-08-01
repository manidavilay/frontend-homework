<template>
  <size-calculator></size-calculator>
</template>

<script>
import SizeCalculator from './components/SizeCalculator.vue'

export default {
  name: 'App',
  components: {
    SizeCalculator
  }
}
</script>

<style lang="scss">
@import './styles/app.scss';

#app {
  @include flex-center();
  width: 100%;
  height: 100vh;
}
</style>