<script>
    import { ethers } from "./ethers-5.2.umd.min.js";

    export default {
        data() {
            return {
                signerBalance: '',
                network: '',
                addressTo: '',
                amountTo: '',
                transactionHash: 0
            }
        },
        methods: {
            async getBalance() {
                const provider = new ethers.providers.Web3Provider(window.ethereum);

                await provider.send("eth_requestAccounts", []);

                this.network = (await provider.getNetwork()).name;

                const signer = provider.getSigner();

                let accauntBalance = await signer.getBalance();
                
                this.signerBalance = ethers.utils.formatEther(accauntBalance);
            },
            async transferTo() {
                const provider = new ethers.providers.Web3Provider(window.ethereum);
                await provider.send("eth_requestAccounts", []);

                const signer = provider.getSigner();

                const _amount = (ethers.utils.parseEther((this.amountTo).toString())).toHexString();

                const params = [{
                    from: await signer.getAddress(),
                    to: this.addressTo,
                    value: _amount
                }]

                this.transactionHash = await provider.send('eth_sendTransaction', params);
            }
        },
        mounted() {
            this.getBalance();
            ethereum.on('accountsChanged', this.getBalance);
            ethereum.on('chainChanged', this.getBalance);
        }
    }

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