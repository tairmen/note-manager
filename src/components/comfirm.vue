<template>
  <transition name="modal">
    <div class="modal-mask">
      <div class="modal-wrapper">
        <div class="modal-container">
          <div class="modal-header">
            <slot name="header">{{title}}</slot>
          </div>
          <div class="modal-body">
            <slot name="body">{{msg}}</slot>
          </div>
          <div class="modal-footer">
            <slot name="footer">
              <button class="modal-button" @click="$emit('confirm')">Yes</button>
              <button class="modal-button" @click="$emit('close')">No</button>
            </slot>
          </div>
        </div>
      </div>
    </div>
  </transition>
</template>

<script>
export default {
  name: "confirm",
  props: {
    title: null,
    msg: null
  },
  data() {
    return {
      
    };
  },
  methods: {
    
  }
};
</script>

<style scoped>
.modal-mask {
  position: fixed;
  z-index: 9998;
  top: 0;
  left: 0;
  width: 100%;
  height: 100%;
  background-color: rgba(0, 0, 0, 0.5);
  display: table;
  transition: opacity 0.3s ease;
}
.modal-wrapper {
  display: table-cell;
  vertical-align: middle;
}
.modal-container {
  max-width: 300px;
  margin: 0px auto;
  padding: 20px 30px;
  background-color: #fff;
  border-radius: 2px;
  box-shadow: 0 2px 8px rgba(0, 0, 0, 0.33);
  transition: all 0.3s ease;
  font-family: Helvetica, Arial, sans-serif;
}
.modal-header {
  font-size: 20px;
  text-align: center;
}
.modal-body {
  margin: 20px 0;
}
.modal-button {
  color: black;
  background-color: white; 
  padding: 6px 12px;
  text-align: center;
  text-decoration: none;
  display: inline-block;
  font-size: 15px;
  margin: 4px 16px 4px 2px;
  cursor: pointer;
  border: 1px solid #707070;
  transition-duration: 0.3s;
}
.modal-button:hover {
  background-color: #707070; 
  color: white;
}
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
