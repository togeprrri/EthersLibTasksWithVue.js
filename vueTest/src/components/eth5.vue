<script setup>
    import { ethers } from "./ethers-5.2.umd.min.js";
    import { ref, onMounted } from 'vue'

    const network = ref('');
    const tokenAddress = ref('');

    const tokenInfo = ref({
        name: '',
        symbol: '',
        balance: ''
    })

    const addresses = ref([]);
    const tempAddress = ref('');

    let tokenContract;

    async function connectToNetwork(){
        const provider = new ethers.providers.Web3Provider(window.ethereum);

        await provider.send("eth_requestAccounts", []);

        network.value = (await provider.getNetwork()).name;
    }

    async function tokenAddressConnect(){
        if((tokenAddress.value).length != 42 || tokenAddress.value.slice(0,2)!='0x'){
            return;
        }

        const provider = new ethers.providers.Web3Provider(window.ethereum);

        await provider.send("eth_requestAccounts", []);

        const signer = provider.getSigner();


        const tokenAbi = [
            "function name() view returns (string)",
            "function symbol() view returns (string)",

            "function balanceOf(address) view returns (uint)",

            "function transfer(address to, uint amount)",
        ]

        tokenContract = new ethers.Contract(tokenAddress.value, tokenAbi, signer);

        tokenInfo.value.name = await tokenContract.name();
        tokenInfo.value.symbol = await tokenContract.symbol();
        tokenInfo.value.balance = ethers.utils.formatEther(await tokenContract.balanceOf(signer.getAddress()));
    }

    async function addAddress(){
        const tempAmount = await tokenContract.balanceOf(tempAddress.value);
        addresses.value.push({
            address: tempAddress.value, 
            amount: ethers.utils.formatEther(tempAmount) + " " + tokenInfo.value.symbol});
    }

    function removeAddress(adr){
        addresses.value = addresses.value.filter((a) => a!==adr);
    }

    onMounted(() => {
        connectToNetwork();
    })

</script>

<template>
    <div class = "body">
        <div class = "block">
            <h2>Network: {{network}}</h2>
            <h2>Input token address:</h2>
            <input id="address" type="text" v-model="tokenAddress" @input="tokenAddressConnect" placeholder="Token address">
            <br><br>
            <h2>Connected token:</h2>
            <h4>Name: {{tokenInfo.name}}</h4>
            <h4>Symbol: {{tokenInfo.symbol}}</h4>
            <h4>Your balance: {{tokenInfo.balance}}</h4>
        </div>
        <div class = "block">
            <h2>Input addresses you want to check:</h2>
            <input id="address" type="text" v-model="tempAddress" placeholder="Address"><br>
            <button @click="addAddress">Add</button>
        </div>
        <div class = "block" id = "addressesList">
            <h2 v-if="addresses.length">Addresses:</h2>
            <ul>
                <li v-for="adr in addresses">
                    <u>Address:</u> {{adr.address}}<br>
                    <u>Amount:</u> {{adr.amount}}<br>
                    <button @click="removeAddress(adr)">Remove</button>
                </li>
            </ul>
        </div>
    </div>
    
</template>

<style>
.body {
    display: flex;
}
.block {
    margin: 0 100px 0 0;
    width: 300px;
}

#address {
    width: 320px;
}

#addressesList {
    width: 450px;
}
</style>