<script setup>
  import { ref } from 'vue'
  import eth1 from './components/eth1.vue'
  import eth2 from './components/eth2.vue'

const taskId = ref(1);

function prev() {
  if(taskId.value != 1){
    taskId.value--;
  }
}

function next() {
  taskId.value++;
}
</script>

<template>
  <main>
    <div v-if="taskId === 1">
      <h1>Task 1</h1>
      <eth1 />
    </div>
    <div v-else-if="taskId === 2">
      <h1>Task 2</h1>
      <eth2 />
    </div>
    <br><br>
    <button @click="prev">Prev</button>
    <button @click="next">Next</button>
  </main>
</template>

