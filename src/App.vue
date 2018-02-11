<template>
  <div id="app">
    <div class="header container">
      <h1>Fallout Terminal</h1>
      <p id="show-modal" @click="showModal = true">What's this?</p>
    </div>

    <modal v-if="showModal" @close="showModal = false">
      <h2 class="green" slot="header">Fallout Terminal</h2>

      <img slot="body" src="/static/img/vaultboy.png" alt="Vault Boy" width="48px" height="48px">
      <p slot="body">Inspired by the popular video game series <i><a href="https://en.wikipedia.org/wiki/Fallout_(series)" target="_blank">Fallout</a></i>, this fun hacking game requires the user to guess a randomly selected password to gain access to the terminal.</p>
      <p slot="body">Use the arrow keys on your keyboard to use the app and the enter key to make a selection. You have 4 attempts to guess correctly!</p>
      <p slot="body"><i>Hint: Find matching pairs of brackets in the text (e.g. <strong>[</strong>,>;<strong>]</strong>) to remove dud words!</i></p>
      <h2 slot="body" class="green">Vue.js</h2>
      <img slot="body" height="48px" width="48px" src="/static/img/vue.svg" alt="Vue.js">
      <p slot="body">This project was built using <a href="https://vuejs.org/" target="_blank">Vue.js</a> as a means of helping me become more familiar with the framework, and concepts such as templating, transitions, reusable single-file components, and more.</p>
      <p slot="body">To gain more insight in to this project you can view it on <a href="https://github.com/bjbancroft19/Fallout-Terminal-2" target="_blank">GitHub</a>. If you would like to see more of my work please <a href="https://benjaminbancroft.co.uk/" target="_blank">visit my website</a>.</p>

      <p slot="footer">Ben Bancroft | 2018</p>
      <p class="modal-social-links" slot="footer">
        <a href="https://github.com/bjbancroft19" target="_blank"><icon name="github"></icon></a>
        <a href="https://www.linkedin.com/in/ben-bancroft-b07058115/" target="_blank"><icon name="linkedin"></icon></a>
        <a href="mailto:bjbancroft19@gmail.com"><icon name="envelope"></icon></a>
      </p>
    </modal>

    <Terminal/>
  </div>
</template>

<script>
import 'vue-awesome/icons';
import Icon from 'vue-awesome/components/Icon';
import Terminal from './components/Terminal';
import Modal from './components/Modal';

export default {
  name: 'App',
  components: {
    Terminal,
    Modal,
    Icon
  },
  data() {
    return {
      showModal: false
    };
  }
};
</script>

<style lang="scss">
$lightgreen: #4afa8f;
$darkgreen: #008f3b;
.green { color: $darkgreen; }
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
.modal-container {
  a {
    color: $darkgreen;
    font-weight: 700;
    text-decoration: none;
  }
  p { margin-top: 14px; }
  .modal-header {
    h2 { margin: 0; }
  }
  .modal-footer {
    .modal-social-links {
      margin-top: 10px;
      .fa-icon {
        color: $darkgreen;
        width: 24px;
        height: auto;
        margin: 0 5px;
      }
    }
    p { margin-top: 0; }
    &:before {
      background: -webkit-gradient(linear, left top, left bottom, color-stop(50%, #444), color-stop(50%, #000));
      background: linear-gradient(#444 50%, #000 50%);
      background-size: 100% 4px;
      background-repeat: repeat-y;
      opacity: 0.2;
      height: 100%;
      width: 100%;
      position: absolute;
      content: "";
      top: 0;
      left: 0;
      z-index: -1;
    }
  }
}
</style>
