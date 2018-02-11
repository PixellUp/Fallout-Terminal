<template>
  <div id="app">
    <div class="header container">
      <h1>Fallout Terminal</h1>
      <p id="show-modal" @click="showModal = true">What's this?</p>
    </div>

    <modal v-if="showModal" @close="showModal = false">
      <h3 slot="header">The modal</h3>
    </modal>

    <Terminal/>
  </div>
</template>

<script>
import Terminal from './components/Terminal';
import Modal from './components/Modal';

export default {
  name: 'App',
  components: {
    Terminal,
    Modal
  },
  data() {
    return {
      showModal: false
    };
  }
};
</script>

<style lang="scss">
.header {
  padding: 10px 0;
  display: flex;
  flex-direction: row;
  justify-content: space-between;
  h1 {
    font-size: 1.6em;
    margin: 0;
  }
  p {
    align-self: center;
    cursor: pointer;
  }
}
</style>
