<script setup>
const props = defineProps(['title', 'min', 'max']);
const model = defineModel();

function checkValue() {
  if (model.value > props.max) {
    model.value = props.max;
  } else if (model.value < props.min) {
    model.value = props.min;
  }
}
</script>

<template>
  <p class="range-input">
    {{ title }}:
    <input type="number" v-model="model" :max="max" :min="min" @change="checkValue">
    <input type="range" v-model="model" :max="max" :min="min">
  </p>
</template>

<style scoped>
.range-input {
  background-color: var(--light-bg);
  border: 1px solid var(--border-color);
  padding: 15px;
  border-radius: 5px;
  margin-bottom: 15px;
  box-shadow: var(--shadow);
}

.range-input input[type="number"],
.range-input input[type="range"] {
  margin-top: 5px;
}
</style>
