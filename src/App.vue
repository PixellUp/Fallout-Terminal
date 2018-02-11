<template>
  <div id="app">

    <Terminal/>
  </div>
</template>

<script>
import Terminal from './components/Terminal';

export default {
  name: 'App',
  components: {
    Terminal
  }
};
</script>

<style lang="scss">
body {
  background-color: #787878;
}
</style>
