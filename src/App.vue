<script setup>
import { computed, onMounted, ref } from 'vue';
import RangeInput from './components/RangeInput.vue';
import BudgetPanel from './components/BudgetPanel.vue';
import MapDestination from './components/MapDestination.vue';

const persons = ref(2);
const days = ref(7);
const chosenAccommodation = ref('Hostal');
const accommodationTypes = ["Albergue", "Hostal", "Bed and Breakfast", "Hotel 3*", "Hotel Superior" ];

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

const costEstimate = computed(()=>{
  return days.value * persons.value * costsAccommodations[chosenAccommodation.value] * costsDestinations[chosenDestination.value.economic_level];
});

onMounted(async ()=>{
  const response = await fetch('http://localhost:3000/api/destinations');
  const data = await response.json();
  destinations.value = data.sort((a,b) => a.name<b.name ? -1 : 1);
})
</script>

<template>
<h1>Destinos turísticos</h1>
<p>{{ persons }} personas durante {{ days }} días en {{ chosenDestination.name }} alojandose en {{ chosenAccommodation }}</p>
<h3 v-if="costEstimate">Se estima {{ costEstimate.toFixed(2) }}€</h3>
<BudgetPanel :costEstimate="costEstimate"/>
<RangeInput title="Número de personas" v-model="persons" :min="1" :max="10" />
<RangeInput title="Número de días" v-model="days" :min="1" :max="30" />
<p>Tipo de alojamiento: 
  <select v-model="chosenAccommodation">
    <option v-for="(accommodation,index) in accommodationTypes" :key="index">{{ accommodation }}</option>
  </select>
</p>
<MapDestination v-if="chosenDestination"
  :lat="chosenDestination.coordinates.latitude"
  :lon="chosenDestination.coordinates.longitude"
  :zoom="2"
  />
<p>Selecciona un destino:
  <ul>
    <li v-for="destination in destinations" 
    :key="destination.id"
    :class="destination.economic_level"
    @click="chosenDestination=destination"
    >{{ destination.name }}</li>
  </ul>
</p>
</template>

<style>
  input[type="number"] {
    text-align: right;
  }
  
  .cheap {
    color: darkgreen;
  }

  .moderate {
    color: darkorange
  }

  .expensive {
    color: red;
  }


</style>
