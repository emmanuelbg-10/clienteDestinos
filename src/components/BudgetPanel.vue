<script setup>
import { onMounted, ref, watch } from 'vue';

const budget = ref(0);
defineProps(['costEstimate']);

watch(budget, (newBudget) => {
  localStorage.budget = newBudget;
});

onMounted(() => {
  budget.value = localStorage.budget ?? 0;
});
</script>

<template>
  <div class="budget-panel">
    <p>Indica tu presupuesto</p>
    <input type="number" v-model="budget">
    <p v-if="budget <= 0" class="warning">Debe indicar un presupuesto mayor que 0</p>
    <h2 v-else>
      Su presupuesto es
      <span :class="{
        expensive: costEstimate > budget,
        moderate: costEstimate > budget * 0.9,
        cheap: costEstimate <= budget * 0.9
      }">
        {{ budget }}
      </span>
      €
    </h2>
  </div>
</template>

<style scoped>
.budget-panel {
  background-color: var(--light-bg);
  border: 1px solid var(--border-color);
  padding: 15px;
  border-radius: 5px;
  margin: 20px 0;
  box-shadow: var(--shadow);
  text-align: center;
}

.budget-panel input[type="number"] {
  width: 100%;
  max-width: 200px;
  padding: 10px;
  border: 1px solid var(--border-color);
  border-radius: 5px;
  margin-top: 5px;
}

.budget-panel .warning {
  color: red;
  margin-top: 10px;
}
</style>
