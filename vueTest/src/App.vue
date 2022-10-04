<script setup>
  import { ref } from 'vue'
  import eth1 from './components/eth1.vue'
  import eth2 from './components/eth2.vue'
  import eth4 from './components/eth4.vue'
  import eth5 from './components/eth5.vue'
  import eth1_o from './components/eth1_o.vue'
  import eth2_o from './components/eth2_o.vue'

const taskId = ref(1);

const tasks = [
  0, eth1_o, eth2_o, eth4, eth5
]

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
    <div>
      <h1>Task {{taskId}}</h1>
      <component :is="tasks[taskId]"></component>
    </div>
    <br><br>
    <button @click="prev">Prev</button>
    <button @click="next">Next</button>
  </main>
</template>

