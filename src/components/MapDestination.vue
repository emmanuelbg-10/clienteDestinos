<script setup>
import { computed } from 'vue';

const props = defineProps(['lat', 'lon', 'zoom']);

const minLat = computed(() => {
  return props.lat-props.zoom;
});

const maxLat = computed(() => {
  return props.lat+props.zoom;
});

const minLon = computed(() => {
  return props.lon-props.zoom;
});

const maxLon = computed(() => {
  return props.lon+props.zoom;
});

const url = computed(()=>{
  return `https://www.openstreetmap.org/export/embed.html?bbox=${minLon.value},${minLat.value},${maxLon.value},${maxLat.value}&amp;layer=mapnik`;
})
</script>

<template>
  <iframe width="425" height="350"
    :src="url"
    style="border: 1px solid black"></iframe><br />
</template>

<style scoped></style>