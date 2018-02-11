<template>
  <transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">

          <button class="modal-close-button" @click="$emit('close')">
            <icon name="times"></icon>
          </button>

          <div class="modal-header">
            <slot name="header">
              default header
            </slot>
          </div>

          <div class="modal-body">
            <slot name="body">
              default body
            </slot>
          </div>

          <div class="modal-footer">
            <slot name="footer">
              default footer
            </slot>
          </div>

        </div>
      </div>
    </div>
  </transition>
</template>

<script>
import 'vue-awesome/icons';
import Icon from 'vue-awesome/components/Icon';

export default {
  name: 'Modal',
  components: {
    Icon
  }
};
</script>

<style lang="scss" scoped>
$darkgreen: #008f3b;
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, .5);
  display: table;
  transition: opacity .3s ease;
}

.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}

.modal-container {
  position: relative;
  color: #3c3c3c;
  text-align: center;
  font-size: 14px;
  width: 500px;
  margin: 0px auto;
  border-radius: 10px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, .33);
  transition: all .3s ease;
  font-family: 'Open Sans', Arial, sans-serif;
}

.modal-header {
  padding: 20px 30px 10px 30px;
  background-color: #fff;
  border-radius: 10px 10px 0 0;
}

.modal-body {
  padding: 0 30px 20px 30px;
  background-color: #fff;
}

.modal-close-button {
  background-color: transparent;
  border: none;
  position: absolute;
  right: 20px;
  top: 20px;
  padding: 0;
  .fa-icon {
    color: #fff;
    width: 16px;
    height: 16px;
    background-color: $darkgreen;
    border-radius: 50%;
    padding: 5px;
    font-weight: 300;
  }
}

.modal-footer {
  background-color: rgba(0,0,0,0.5);
  padding: 20px 30px;
  color: #fff;
}

/*
 * The following styles are auto-applied to elements with
 * transition="modal" when their visibility is toggled
 * by Vue.js.
 *
 * You can easily play with the modal transition by editing
 * these styles.
 */

.modal-enter {
  opacity: 0;
}

.modal-leave-active {
  opacity: 0;
}

.modal-enter .modal-container,
.modal-leave-active .modal-container {
  -webkit-transform: scale(1.1);
  transform: scale(1.1);
}
</style>
