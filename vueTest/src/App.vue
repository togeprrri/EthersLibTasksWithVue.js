<script setup>
  import { ref } from 'vue'
  import eth1 from './components/eth1.vue'
  import eth2 from './components/eth2.vue'
  import eth4 from './components/eth4.vue'
  import eth5 from './components/eth5.vue'

const taskId = ref(1);

function prev() {
  if(taskId.value != 1){
    taskId.value--;
  }
}

function next() {
  if(taskId.value != 4){
    taskId.value++;
  }
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
    <div v-else-if="taskId === 3">
      <h1>Task 4</h1>
      <eth4 />
    </div>
    <div v-else-if="taskId === 4">
      <h1>Task 5</h1>
      <eth5 />
    </div>
    <br><br>
    <button @click="prev">Prev</button>
    <button @click="next">Next</button>
  </main>
</template>

