<script setup>
const props = defineProps(['title', 'min', 'max']);
const model = defineModel();

function checkValue() {
  if (model.value>props.max) {
    model.value = props.max;
  }
  else if (model.value<props.min) model.value = props.min;
};
</script>

<template>
<p>{{title}}:
  <input type="number" v-model="model" :max="max" :min="min" @change="checkValue">
  <input type="range" v-model="model" :max="max" :min="min">
</p>
</template>

<style scoped>
p {
  background-color: antiquewhite;
}
</style>