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

    const transactions = ref([]);
    const tempAddress = ref('');
    const tempAmount = ref(null);

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

    function addTransaction(){
        transactions.value.push({address: tempAddress.value, amount: tempAmount.value});
    }

    function removeTransaction(tx){
        transactions.value = transactions.value.filter((t) => t!==tx);
    }

    async function transferTokensToAccounts() {
        const provider = new ethers.providers.Web3Provider(window.ethereum);

        await provider.send("eth_requestAccounts", []);

        const signer = provider.getSigner();

        transactions.value.forEach(element => {
            tokenContract.transfer(element.address, ethers.utils.parseEther((element.amount).toString()));
        });
    };

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
            <h2>Input transaction properties:</h2>
            <input id="address" type="text" v-model="tempAddress" placeholder="Address"><br>
            <input type="number" v-model="tempAmount" placeholder="Amount">
            <button @click="addTransaction">Add</button>
            <br><br>
            <button @click="transferTokensToAccounts">Confirm transactions</button>
        </div>
        <div class = "block" id = "transactionList">
            <h2 v-if="transactions.length">Transactions:</h2>
            <ul>
                <li v-for="tx in transactions">
                    <u>Address:</u> {{tx.address}}<br>
                    <u>Amount:</u> {{tx.amount}} {{tokenInfo.symbol}}<br>
                    <button @click="removeTransaction(tx)">Remove</button>
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

#transactionList {
    width: 450px;
}
</style>