<script setup lang="ts">
import { ref, watch } from 'vue';
import axios from 'axios';
let counts = ref(0)
let value = ref('')

const scriptsMrb = ref([
    { name: 'auto donate', price: 1990, amount: 0, count: 0 },
    { name: 'jail', price: 1499, amount: 0, count: 0 },
    { name: 'banking', price: 1599, amount: 0, count: 0 },
    { name: 'economy', price: 1550, amount: 0, count: 0 },
    { name: 'mail', price: 999, amount: 0, count: 0 },
])
const scriptsFunny = ref([
    { name: 'auto donate', price: 1990, amount: 0, count: 0 },
    { name: 'jail', price: 1499, amount: 0, count: 0 },
    { name: 'banking', price: 1599, amount: 0, count: 0 },
    { name: 'economy', price: 1550, amount: 0, count: 0 },
    { name: 'mail', price: 999, amount: 0, count: 0 },
])

let billMrb = ref({
    price: 0,
    total: 0,
    receivedAmount: 0
})
let billFunny = ref({
    price: 0,
    total: 0,
    receivedAmount: 0
})


const updateAmountMrb = (index: number, count: number) => {
    // let rawValue = event.target.value.replace(/[^0-9]/g, '');
    // event.target.value = rawValue
    let amount = count

    !amount ? scriptsMrb.value[index].amount = 0 : scriptsMrb.value[index].amount = scriptsMrb.value[index].price * amount

    // scriptsMrb.value[index].amount = scriptsMrb.value[index].price * amount;

    let prices = 0
    let totals = 0
    scriptsMrb.value.forEach((script) => {
        if (script.amount != 0) {
            prices += script.amount
            totals = (prices * 60) / 100

            billMrb.value.price = prices
            billMrb.value.total = Math.round(totals)
            billMrb.value.receivedAmount = Math.round(prices - totals)


        }
    })
    if (scriptsMrb.value[0].amount === 0 && scriptsMrb.value[1].amount === 0 && scriptsMrb.value[2].amount === 0 && scriptsMrb.value[3].amount === 0) {
        billMrb.value.price = 0
        billMrb.value.total = 0
        billMrb.value.receivedAmount = 0
    }
    onCheckPrice()

};


const updateAmountFunny = (index: number, count: number) => {
    // let rawValue = event.target.value.replace(/[^0-9]/g, '');
    // event.target.value = rawValue
    let amount = count

    !amount ? scriptsFunny.value[index].amount = 0 : scriptsFunny.value[index].amount = scriptsFunny.value[index].price * amount

    let prices = 0
    let totals = 0
    scriptsFunny.value.forEach((script) => {
        if (script.amount != 0) {
            prices += script.amount
            totals = (prices * 40) / 100

            billFunny.value.price = prices
            billFunny.value.total = Math.round(prices - totals)
            billFunny.value.receivedAmount = Math.round(totals)
        }
    })
    if (scriptsFunny.value[0].amount === 0 && scriptsFunny.value[1].amount === 0 && scriptsFunny.value[2].amount === 0 && scriptsFunny.value[3].amount === 0) {
        billFunny.value.price = 0
        billFunny.value.total = 0
        billFunny.value.receivedAmount = 0
    }
    onCheckPrice()

};

const onCheckPrice = () => {
    if (billMrb.value.receivedAmount > billFunny.value.receivedAmount) {
        counts.value = Math.round(billMrb.value.receivedAmount - billFunny.value.receivedAmount);
        console.log('FUNNY จะได้รับเงินจำนวน : ', counts.value)
        value.value = 'FUNNY จะได้รับเงินจำนวน ' + counts.value + ' บาท'

    } else if (billMrb.value.receivedAmount < billFunny.value.receivedAmount) {
        counts.value = Math.round(billFunny.value.receivedAmount - billMrb.value.receivedAmount);
        console.log('MRB จะได้รับเงินจำนวน  : ', counts.value)
        value.value = 'MRB จะได้รับเงินจำนวน ' + counts.value + ' บาท'

    } else if (billMrb.value.receivedAmount === billFunny.value.receivedAmount) {
        value.value = 'MRB และ FUNNY จะได้รับเงินจำนวนเท่ากัน'
        console.log('จะได้รับเงินจำนวนเท่ากัน')
    }
}

const getSql = () => {
    let config = {
        method: 'get',
        maxBodyLength: Infinity,
        url: 'https://api.mrb-developer.com/service_for_project/getpartnersell',
        headers: {}
    };

    axios.request(config)
        .then((response) => {
            const scripts = response.data
            scripts.forEach((element: any) => {
                if (element.seller === 'MRB SHOP') {
                    if (element.name === 'autodonate') {
                        scriptsMrb.value[0].count = element.count
                        updateAmountMrb(0, element.count)
                    } else if (element.name === 'Jail') {
                        scriptsMrb.value[1].count = element.count
                        updateAmountMrb(1, element.count)
                    } else if (element.name === 'banking') {
                        scriptsMrb.value[2].count = element.count
                        updateAmountMrb(2, element.count)
                    } else if (element.name === 'EconomyX') {
                        scriptsMrb.value[3].count = element.count
                        updateAmountMrb(3, element.count)
                    } else if (element.name === 'mail') {
                        scriptsMrb.value[4].count = element.count
                        updateAmountMrb(4, element.count)
                    }

                } else if (element.seller === 'Funny Production') {
                    if (element.name === 'autodonate') {
                        scriptsFunny.value[0].count = element.count
                        updateAmountFunny(0, element.count)
                    } else if (element.name === 'Jail') {
                        scriptsFunny.value[1].count = element.count
                        updateAmountFunny(1, element.count)
                    } else if (element.name === 'banking') {
                        scriptsFunny.value[2].count = element.count
                        updateAmountFunny(2, element.count)
                    } else if (element.name === 'EconomyX') {
                        scriptsFunny.value[3].count = element.count
                        updateAmountFunny(3, element.count)
                    } else if (element.name === 'mail') {
                        scriptsFunny.value[4].count = element.count
                        updateAmountFunny(4, element.count)
                    }
                }
            });
            // updateAmountMrb()
            console.log(response.data);
        })
        .catch((error) => {
            console.log(error);
        });
}

const onClear = () => {
    scriptsMrb.value.map((script, index) => {
        script.count = 0
        updateAmountMrb(index, 0)
    })

    scriptsFunny.value.map((script, index) => {
        script.count = 0
        updateAmountFunny(index, 0)
    })
}
</script>
<template>
    <div class="w-2/5 h-96 mx-auto pt-10">
        <p class="font-bold text-xl">MRB</p>
        <div class="grid grid-cols-12">
            <div class="col-span-6">
                <span v-for="(script, index) in scriptsMrb" :key="index"
                    class="grid grid-cols-12 items-center gap-2 mt-2">
                    <p class="col-span-3">{{ script.name }}</p>
                    <input v-model="script.count" type="text"
                        class="col-span-2 bg-gray-600 rounded-md outline-none p-1 text-center w-10"
                        @input="updateAmountMrb(index, script.count)">
                    <p class="col-span-2">{{ script.price }}</p>
                    <p class="col-span-2">{{ script.amount }}</p>
                    <p class="ccol-span-2 text-[#A3FFD6]" v-if="script.count > 0">{{ script.count }}</p>
                </span>
            </div>
            <div class="col-span-6">
                <div class="grid grid-cols-12 bg-gray-600 rounded-xl p-3">
                    <span class="col-span-8 pl-3">
                        <p>ราคารวม</p>
                        <p>MRB จะได้รับเงิน</p>
                        <p>FUNNY จะได้รับเงิน</p>
                    </span>
                    <span class="col-span-4 text-end pr-3">
                        <p class="font-bold">{{ billMrb.price }} บาท</p>
                        <p class="font-bold">{{ billMrb.total }} บาท</p>
                        <p class="font-bold">{{ billMrb.receivedAmount }} บาท</p>
                    </span>
                </div>
                <p class="mt-4 font-bold text-orange-200 text-end">MRB จะให้เงิน FUNNY จำนวน {{ billMrb.receivedAmount
                    }} บาท</p>
            </div>
        </div>

        <p class="font-bold text-xl mt-10">FUNNY</p>
        <div class="grid grid-cols-12">
            <div class="col-span-6">
                <span v-for="(script, index) in scriptsFunny" :key="index"
                    class="grid grid-cols-12 items-center gap-2 mt-2">
                    <p class="col-span-3">{{ script.name }}</p>
                    <input v-model="script.count" type="text"
                        class="col-span-2 bg-gray-600 rounded-md outline-none p-1 text-center w-10"
                        @input="updateAmountFunny(index, script.count)">
                    <p class="col-span-2">{{ script.price }}</p>
                    <p class="col-span-2">{{ script.amount }}</p>
                    <p class="col-span-2 text-[#A3FFD6]" v-if="script.count > 0">{{ script.count }}</p>
                </span>
            </div>
            <div class="col-span-6">
                <div class="grid grid-cols-12 bg-gray-600 rounded-xl p-3">
                    <span class="col-span-8 pl-3">
                        <p>ราคารวม</p>
                        <p>FUNNY จะได้รับเงิน</p>
                        <p>MRB จะได้รับเงิน</p>
                    </span>
                    <span class="col-span-4 text-end pr-3">
                        <p class="font-bold">{{ billFunny.price }} บาท</p>
                        <p class="font-bold">{{ billFunny.total }} บาท</p>
                        <p class="font-bold">{{ billFunny.receivedAmount }} บาท</p>
                    </span>
                </div>
                <p class="mt-4 font-bold text-orange-200 text-end">FUNNY จะให้เงิน MRB จำนวน {{ billFunny.receivedAmount
                    }} บาท</p>
            </div>
        </div>
        <br>
        <button v-on:click="getSql()" class="bg-blue-500 hover:bg-blue-700 text-white font-bold py-2 px-4 rounded-md">
            โหลด SQL
        </button>
        <button v-on:click="onClear()"
            class="bg-red-600 hover:bg-red-700 text-white font-bold py-2 px-4 rounded-md ml-3">
            Clear All
        </button>
        <div class="mt-10 text-center text-amber-100 font-bold text-xl">{{ value }}</div>
    </div>
</template>