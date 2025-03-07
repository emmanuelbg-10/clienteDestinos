<script setup>
import { computed } from 'vue';

const props = defineProps(['lat', 'lon', 'zoom']);

const minLat = computed(() => props.lat - props.zoom);
const maxLat = computed(() => props.lat + props.zoom);
const minLon = computed(() => props.lon - props.zoom);
const maxLon = computed(() => props.lon + props.zoom);

const url = computed(() => {
  return `https://www.openstreetmap.org/export/embed.html?bbox=${minLon.value},${minLat.value},${maxLon.value},${maxLat.value}&amp;layer=mapnik`;
});
</script>

<template>
  <iframe :src="url" title="Mapa de destino"></iframe>
</template>

<style scoped>
iframe {
  width: 100%;
  max-width: 600px;
  height: 350px;
  border: none;
  border-radius: 8px;
  box-shadow: var(--shadow);
  margin: 20px auto;
  display: block;
  pointer-events: none;
  /* Desactiva la interacción */
}
</style>
