<script setup>
import { computed, onMounted, ref } from 'vue';
import RangeInput from './components/RangeInput.vue';
import BudgetPanel from './components/BudgetPanel.vue';
import MapDestination from './components/MapDestination.vue';

const persons = ref(2);
const days = ref(7);
const chosenAccommodation = ref('Hostal');
const accommodationTypes = ["Albergue", "Hostal", "Bed and Breakfast", "Hotel 3*", "Hotel Superior"];

const costsDestinations = {
  cheap: 0.7,
  moderate: 1,
  expensive: 1.3
}

const costsAccommodations = {
  "Albergue": 25,
  "Hostal": 40,
  "Bed and Breakfast": 65,
  "Hotel 3*": 100,
  "Hotel Superior": 200,
}

const destinations = ref([]);
const chosenDestination = ref('');

const costEstimate = computed(() => {
  return days.value * persons.value * costsAccommodations[chosenAccommodation.value] * costsDestinations[chosenDestination.value.economic_level];
});

onMounted(async () => {
  const response = await fetch('https://serverdestino.vercel.app/api/destinations');
  const data = await response.json();
  destinations.value = data.sort((a, b) => a.name < b.name ? -1 : 1);
});
</script>

<template>
  <div class="container">
    <h1>Destinos turísticos</h1>
    <p class="intro">
      {{ persons }} personas durante {{ days }} días en {{ chosenDestination.name }} alojándose en {{
        chosenAccommodation }}
    </p>
    <h3 v-if="costEstimate">Se estima <span class="estimate">{{ costEstimate.toFixed(2) }}€</span></h3>
    <BudgetPanel :costEstimate="costEstimate" />
    <div class="controls">
      <RangeInput title="Número de personas" v-model="persons" :min="1" :max="10" />
      <RangeInput title="Número de días" v-model="days" :min="1" :max="30" />
    </div>
    <p class="select-accommodation">
      Tipo de alojamiento:
      <select v-model="chosenAccommodation">
        <option v-for="(accommodation, index) in accommodationTypes" :key="index">{{ accommodation }}</option>
      </select>
    </p>
    <MapDestination v-if="chosenDestination" :lat="chosenDestination.coordinates.latitude"
      :lon="chosenDestination.coordinates.longitude" :zoom="2" />
    <p class="destinations-label">Selecciona un destino:</p>
    <ul class="destinations">
      <li v-for="destination in destinations" :key="destination.id"
        :class="[destination.economic_level, { active: destination.id === chosenDestination.id }]"
        @click="chosenDestination = destination">
        {{ destination.name }}
      </li>
    </ul>
  </div>
</template>

<style>
/* Reset básico */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
}

/* Variables de colores y tipografía */
:root {
  --primary-color: #2c3e50;
  --secondary-color: #3498db;
  --bg-color: #f9f9f9;
  --text-color: #333;
  --border-color: #ddd;
  --light-bg: #fff;
  --shadow: 0 2px 8px rgba(0, 0, 0, 0.1);
}

/* Base global */
body {
  font-family: 'Roboto', sans-serif;
  background-color: var(--bg-color);
  color: var(--text-color);
  line-height: 1.6;
}

/* Contenedor central */
.container {
  max-width: 1200px;
  margin: 20px auto;
  padding: 20px;
}

/* Titulados */
h1 {
  font-size: 2.5rem;
  margin-bottom: 10px;
  color: var(--primary-color);
  text-align: center;
}

.intro {
  margin-bottom: 15px;
  font-size: 1.1rem;
  text-align: center;
}

.estimate {
  font-weight: bold;
}

/* Controles de inputs */
.controls {
  display: flex;
  flex-wrap: wrap;
  gap: 20px;
  justify-content: center;
  margin-bottom: 20px;
}

/* Estilos para formularios */
input[type="number"],
input[type="range"],
select {
  padding: 10px;
  border: 1px solid var(--border-color);
  border-radius: 5px;
  font-size: 1rem;
  width: 100%;
  max-width: 300px;
  margin: 5px 0;
}

/* Lista de destinos */
.destinations {
  list-style: none;
  padding: 0;
  margin-top: 10px;
}

.destinations li {
  padding: 10px;
  margin-bottom: 8px;
  background-color: var(--light-bg);
  border: 1px solid var(--border-color);
  border-radius: 5px;
  cursor: pointer;
  transition: background-color 0.3s ease;
}

.destinations li:hover,
.destinations li.active {
  background-color: #bdc3c7;
}

/* Colores para niveles económicos */
.cheap {
  color: darkgreen;
}

.moderate {
  color: darkorange;
}

.expensive {
  color: red;
}

/* Responsive */
@media (max-width: 768px) {
  h1 {
    font-size: 2rem;
  }

  .controls {
    flex-direction: column;
    align-items: center;
  }
}
</style>
