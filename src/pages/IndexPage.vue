<template>
  <q-page
    v-touch-pan.vertical.prevent.mouse="handlePan"
    class="flex flex-center text-white"
  >
    <div class="row">
      <q-input 
        v-model="name"
        input-class="text-center text-h5 text-white"
        placeholder="Counter"
        color="teal"
        filled
      />
    </div>
    
    <div class="row full-width items-center">
      <div class="col text-center">
        <q-btn icon="remove" @click="decreaseCounter" size="xl" round />
      </div>
      <div class="col text-center text-h2">
        {{ count }}
      </div>
      <div class="col text-center">
        <q-btn icon="add" @click="increaseCounter" size="xl" round />
      </div>
    </div>

    <div class="row">
      <q-btn icon="restart_alt" @click="resetCounter" size="xl" round />
    </div>
  </q-page>
</template>

<style scoped>
.q-page {
  max-width: 700px;
  margin: 0 auto;
}
</style>

<script setup>
import { ref, watch, onMounted } from 'vue';
import { useQuasar } from 'quasar';

const $q = useQuasar();
const name = ref("");
const count = ref(0);

// Load saved data
onMounted(() => {
  const savedData = $q.localStorage.getItem('data');
  if (savedData) {
    name.value = savedData.name || "";
    count.value = savedData.count || 0;
  }
});

// Debounce function 
const debounce = (fn, delay = 500) => {
  let timer;
  return (...args) => {
    clearTimeout(timer);
    timer = setTimeout(() => fn(...args), delay);
  };
};

// Save to localStorage with debounce
const saveToLocalStorage = debounce(() => {
  $q.localStorage.set('data', { name: name.value, count: count.value });
});

// Watch changes
watch(name, saveToLocalStorage);
watch(count, saveToLocalStorage);

const increaseCounter = () => count.value++;
const decreaseCounter = () => { if(count.value > 0) count.value-- };
const resetCounter = () => count.value = 0;

const handlePan = (value) => {
  value.delta.y > 0 ? increaseCounter() : decreaseCounter();
};
</script>
