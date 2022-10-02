<script setup>
    import { ethers } from "./ethers-5.2.umd.min.js";
    import { ref } from 'vue'

    const _address = ref('');
    const resultBalance = ref('');

    async function getBalance() {
        const provider = new ethers.providers.Web3Provider(window.ethereum);

        await provider.send("eth_requestAccounts", []);

        let accauntBalance = await provider.getBalance(_address.value);
        
        resultBalance.value = ethers.utils.formatEther(accauntBalance);
    }

</script>

<template>
    <h1>Check the accaunt ballance:</h1><br>
    <input v-model="_address" placeholder="Input address"><br>
    <button @click="getBalance">Check</button>
    <h3>{{resultBalance}}</h3>
</template>