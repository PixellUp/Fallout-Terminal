<template>
  <div id="app">
    <!-- background effect markup -->
    <div class="terminal-window">
      <div class="terminal-layer"></div>
      <div class="terminal-overlay"></div>
    </div>

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
$lightgreen: #4afa8f;

// blinking cursor
@keyframes blinking {
  from, to { border-color: transparent; }
  50% { border-color: $lightgreen; }
}
.cursor {
  box-sizing: border-box;
  border-left: .5em solid;
  animation: blinking 1s step-end infinite;
  height: 1em;
  width: 0;
}

// terminal pulse effect
@keyframes vline {
  0% { top: 0px;}
  100% { top: 100%;}
}
.terminal-window {
  width: 100%;
  height: 100vh;
  background-color: #212121;
  position: absolute;
  z-index: -9999;
  .terminal-layer {
    width: 100%;
    height: 100vh;
    position: absolute;
    z-index: 4001;
    background: radial-gradient(ellipse at center, #002c12 0%, rgba(64, 64, 64, 0) 80%);
    opacity: .9;
  }
  .terminal-overlay {
    width: 100%;
    height: 100vh;
    z-index: 4100;
    &:before {
      content : '';
      position : absolute;
      top : 0px;
      left: 0px;
      width : 100%;
      height : 5px;
      background : #fff;
      background: linear-gradient(to bottom, rgba(255,0,0,0) 0%,rgba(255,250,250,1) 50%,rgba(255,255,255,0.98) 51%,rgba(255,0,0,0) 100%);
      opacity : .1;
      animation: vline 3s linear infinite;
    }
  }
}
</style>
