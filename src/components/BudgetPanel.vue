<script setup>
import { onMounted, ref, watch } from 'vue';

const budget = ref(0);

defineProps(['costEstimate']);

watch(budget, (newBudget)=>{
  localStorage.budget = newBudget;
})

onMounted(() => {
  budget.value = localStorage.budget ?? 0;
})
</script>

<template>
  <div>
    <p>
      Indica tu presupuesto
      <input type="number" v-model="budget">
    </p>
    <p v-if="budget <= 0">Debe indicar un presupuesto mayor que 0</p>
    <h2 v-else>Su presupuesto es 
      <span :class="{
        expensive: costEstimate>budget,
        moderate: costEstimate>budget*0.9,
        cheap: costEstimate<=budget*0.9
      }">
        {{ budget }}
      </span>
      €  
      </h2>
  </div>
</template>

<style scoped>
  div {
    background-color: aquamarine;
  }
</style>