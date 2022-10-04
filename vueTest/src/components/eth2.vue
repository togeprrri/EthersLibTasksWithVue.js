<script setup>
    import { ethers } from "./ethers-5.2.umd.min.js";
    import { ref, onMounted } from 'vue'

    const signerBalance = ref('');

    const network = ref('');

    const addressTo = ref('');
    const amountTo = ref('');
    const transactionHash = ref(0);

    async function getBalance() {
        const provider = new ethers.providers.Web3Provider(window.ethereum);

        await provider.send("eth_requestAccounts", []);

        network.value = (await provider.getNetwork()).name;

        const signer = provider.getSigner();

        let accauntBalance = await signer.getBalance();
        
        signerBalance.value = ethers.utils.formatEther(accauntBalance);
    }

    async function transferTo() {
        const provider = new ethers.providers.Web3Provider(window.ethereum);
        await provider.send("eth_requestAccounts", []);

        const signer = provider.getSigner();

        console.log(await signer.getAddress());
        console.log(addressTo.value);
        const _amount = (ethers.utils.parseEther((amountTo.value).toString())).toHexString();
        console.log(_amount);

        const params = [{
            from: await signer.getAddress(),
            to: addressTo.value,
            value: _amount
        }]

        transactionHash.value = await provider.send('eth_sendTransaction', params);

        console.log(transactionHash.value);
    };

    ethereum.on('message', getBalance);
    ethereum.on('accountsChanged', getBalance);
    ethereum.on('chainChanged', getBalance);

    onMounted(() => {
        getBalance();
    })

</script>

<template>
    <h1>Network: {{network}}</h1>
    <h1>Your account ballance: {{signerBalance}}</h1>
    <h3>Input receiver and amount of ethers you want to transfer:</h3>
    <input type="text" v-model="addressTo" placeholder="Input address">
    <input type="number" v-model="amountTo" placeholder="Set amount">
    <button @click="transferTo">Transfer</button>
    <h3 v-if="transactionHash"><br>Success! You can check transction on etherscan by hash: {{transactionHash}}</h3>
</template>