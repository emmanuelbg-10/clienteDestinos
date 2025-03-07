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
/* Variables de diseño moderno */
:root {
  --primary-color: #2563eb;
  /* Azul más vibrante */
  --secondary-color: #4f46e5;
  /* Indigo para acentos */
  --accent-color: #f59e0b;
  /* Amarillo dorado para destacar */
  --bg-gradient: linear-gradient(135deg, #f8fafc 0%, #e2e8f0 100%);
  --text-color: #1e293b;
  --border-color: #cbd5e1;
  --light-bg: #ffffff;
  --shadow: 0 4px 6px -1px rgba(0, 0, 0, 0.1), 0 2px 4px -1px rgba(0, 0, 0, 0.06);
  --radius-lg: 12px;
  --radius-md: 8px;
  --transition: all 0.3s cubic-bezier(0.4, 0, 0.2, 1);
}

/* Reset mejorado */
* {
  box-sizing: border-box;
  margin: 0;
  padding: 0;
  scroll-behavior: smooth;
}

body {
  font-family: 'Inter', system-ui, -apple-system, sans-serif;
  background: var(--bg-gradient);
  color: var(--text-color);
  line-height: 1.5;
  -webkit-font-smoothing: antialiased;
}

.container {
  max-width: 1280px;
  margin: 40px auto;
  padding: 0 20px;
}

/* Encabezado destacado */
h1 {
  font-size: 2.8rem;
  font-weight: 800;
  color: var(--primary-color);
  text-align: center;
  margin-bottom: 1.5rem;
  letter-spacing: -0.025em;
  position: relative;
  padding-bottom: 1rem;
}

h1::after {
  content: '';
  position: absolute;
  bottom: 0;
  left: 50%;
  transform: translateX(-50%);
  width: 120px;
  height: 4px;
  background: var(--accent-color);
  border-radius: 2px;
}

.intro {
  font-size: 1.2rem;
  text-align: center;
  margin-bottom: 2rem;
  color: #64748b;
  max-width: 600px;
  margin-left: auto;
  margin-right: auto;
  line-height: 1.7;
}

.estimate {
  color: var(--secondary-color);
  font-weight: 700;
  font-size: 1.8rem;
  position: relative;
  padding: 0 0.25em;
}

.estimate::after {
  content: '💰';
  margin-left: 8px;
  display: inline-block;
  transform: translateY(2px);
}

/* Sección de controles */
.controls {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(300px, 1fr));
  gap: 2rem;
  margin: 3rem 0;
  background: var(--light-bg);
  padding: 2rem;
  border-radius: var(--radius-lg);
  box-shadow: var(--shadow);
}

/* Selector de alojamiento mejorado */
.select-accommodation {
  text-align: center;
  margin: 2rem 0;
  position: relative;
}

select {
  appearance: none;
  background: var(--light-bg) url("data:image/svg+xml;charset=UTF-8,%3csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 24 24' fill='none' stroke='currentColor' stroke-width='2' stroke-linecap='round' stroke-linejoin='round'%3e%3cpolyline points='6 9 12 15 18 9'%3e%3c/polyline%3e%3c/svg%3e") no-repeat right 1rem center/1em;
  padding: 1rem 2.5rem 1rem 1.5rem;
  border: 2px solid var(--border-color);
  border-radius: var(--radius-md);
  font-size: 1rem;
  font-weight: 500;
  transition: var(--transition);
  cursor: pointer;
}

select:focus {
  outline: none;
  border-color: var(--primary-color);
  box-shadow: 0 0 0 3px rgba(37, 99, 235, 0.1);
}

/* Lista de destinos mejorada */
.destinations-label {
  text-align: center;
  font-weight: 600;
  color: var(--primary-color);
  margin: 2rem 0 1rem;
  font-size: 1.25rem;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}

.destinations {
  display: grid;
  grid-template-columns: repeat(auto-fit, minmax(240px, 1fr));
  gap: 1rem;
  margin: 1rem 0 3rem;
}

.destinations li {
  padding: 1.5rem;
  background: var(--light-bg);
  border: 2px solid var(--border-color);
  border-radius: var(--radius-md);
  cursor: pointer;
  transition: var(--transition);
  text-align: center;
  font-weight: 500;
  display: flex;
  align-items: center;
  justify-content: center;
  position: relative;
  overflow: hidden;
}

.destinations li::before {
  content: '';
  position: absolute;
  top: 0;
  left: 0;
  width: 100%;
  height: 4px;
  background: currentColor;
  opacity: 0.2;
}

.destinations li:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow);
  border-color: var(--primary-color);
}

.destinations li.active {
  border-color: var(--primary-color);
  background: rgba(37, 99, 235, 0.05);
  font-weight: 600;
}

/* Mapa interactivo */
.map-container {
  border-radius: var(--radius-lg);
  overflow: hidden;
  box-shadow: var(--shadow);
  margin: 2rem 0;
  border: 2px solid var(--border-color);
  height: 400px;
}

/* Badges económicos mejorados */
.cheap {
  color: #16a34a;
}

.moderate {
  color: #d97706;
}

.expensive {
  color: #dc2626;
}

/* Efectos de hover y transiciones */
button,
.destinations li,
select {
  transition: var(--transition);
}

@media (max-width: 768px) {
  .container {
    margin: 20px auto;
  }

  h1 {
    font-size: 2rem;
  }

  .controls {
    grid-template-columns: 1fr;
    padding: 1.5rem;
  }

  .destinations {
    grid-template-columns: 1fr;
  }
}

/* Animación sutil al cargar */
@keyframes fadeIn {
  from {
    opacity: 0;
    transform: translateY(10px);
  }

  to {
    opacity: 1;
    transform: translateY(0);
  }
}

.container>* {
  animation: fadeIn 0.6s ease-out forwards;
}
</style>
