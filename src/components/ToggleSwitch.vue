<template>
    <label class="switch-wrapper">
      <span v-if="offLabel" style="margin-right: 10px;">{{ offLabel }}</span>
        <div class="switch">
            <input type="checkbox" checked @click="onToggle">
            <span class="slider"></span>
        </div>
        <span>{{ label }}</span> 
    </label>
</template>

<script>
export default {
  props: {
    label: {
      type: String
    },
    offLabel: {
      type: String,
      default: null
    }
  },
  methods: {
      onToggle(event) {
          this.$emit("SwitchToggled", event.target.checked);
      }
  }
}
</script>

<style scoped>

.switch-wrapper{
    display: inline-flex;
    align-items: center;
}

.switch {
  position: relative;
  display: inline-block;
  width: 40px;
  height: 25px;
  margin-right: 10px;
}


.switch input {
  opacity: 0;
  width: 0;
  height: 0;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #ccc;
  -webkit-transition: .4s;
  transition: .4s;
}

.slider:before {
  position: absolute;
  content: "";
  height: 15px;
  width: 15px;
  left: 6px;
  bottom: 5px;
  background-color: white;
  -webkit-transition: .4s;
  transition: .4s;
}

input:checked + .slider {
  background-color: var(--color-green);
}

input:focus + .slider {
  box-shadow: 0 0 6px rgba(0, 0, 0, 0.6);
}

input:checked + .slider:before {
  -webkit-transform: translateX(13px);
  -ms-transform: translateX(13px);
  transform: translateX(13px);
}

.slider {
  border-radius: 34px;
}

.slider:before {
  border-radius: 50%;
}
</style>