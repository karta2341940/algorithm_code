<script setup>
import { ref, onMounted } from 'vue'
const inputString = ref('I LOVE YOU')
let symbols = []
let probilities = []
/**
 * @type {number[]}
 */
let initProbilities = []
const arrProbilities = ref([[]])
const data = ref([[{ "symbol": "", "floor": 0, "ceiling": 0, "probility": 0 }]])

onMounted(() => {
    fieldUpdate()
})

function clearString() {
    inputString.value = ''
    symbols = []
    probilities = []
}

function getSum(array = [], index = 0) {
    let count = 0
    array.forEach((value, arrIndex) => {
        if (arrIndex >= index) return;
        count += value
    })
    return Number(count.toFixed(10))
}



function submit() {
    arrProbilities.value = [[]]
    // 根據輸入字串跑算術運算
    let Rangefloor = 0
    let newWhileRange = 1

    inputString.value.split('').map((value) => value === " " ? "▲" : value).forEach((valueInput, indexInput) => {

        data.value.push([])
        symbols.forEach((value, index) => {
            if (valueInput !== value) return

            newWhileRange = probilities[index + 1] - probilities[index]
            Rangefloor = probilities[index]
            probilities = computing(initProbilities, Rangefloor, newWhileRange)
            // console.log(probilities)
            data.value[indexInput + 1].push({
                symbol: value,
                floor: probilities[index],
                ceiling: probilities[index + 1],
                probility: initProbilities[index]
            })
        })
        Rangefloor = probilities[0]
        newWhileRange = probilities[probilities.length - 1] - probilities[0]
    })
    //console.log(data.value)
}


function computing(initRange = [], floor = 0, newWhileRange = 1) {
    //取得新的縮小後的range
    let newRangeArray = initRange.map(value => {
        let rtn = value * newWhileRange
        return Number(rtn.toFixed(10))
    })
    // console.log(newWhileRange)
    //遞回的加上floor
    let rtnArray = []
    let newFloor = floor

    newRangeArray.forEach((value) => {
        // 將floor加上縮小後的range
        let ceiling = value + newFloor
        ceiling = Number(ceiling.toFixed(10))
        // 記錄下來
        rtnArray.push(Number(ceiling.toFixed(10)))
        // 將ceiling當作下一個floor
        newFloor = ceiling

    })
    console.log(newRangeArray)
    return rtnArray
}

function getInitRange() {
    return symbols.map((value) => {
        let count = 0
        inputString.value.split('').map((value) => value === " " ? "▲" : value).forEach((item) => {
            if (item === value) count++
        })
        return count / inputString.value.length
    })
}

function fieldUpdate() {
    try {
        symbols = inputString.value.split('').map((value) => value === " " ? "▲" : value).filter((item, index, array) => array.indexOf(item) === index)
        probilities = getInitRange()
        // 記錄下初始的機率(Range)
        initProbilities = getInitRange()
        data.value = [[]]
        symbols.forEach((value, index) => {
            data.value[0].push({
                symbol: value,
                floor: getSum(probilities, index),
                ceiling: getSum(probilities, index + 1),
                probility: probilities[index]
            })
        })
    }
    catch (e) {
        inputString.value = ''
        symbols = []
        probilities = []
        data.value = []
    }
}
</script>
<template>
    <v-container>
        <v-row align="center">
            <v-col cols="8">
                <v-text-field label="String" clearable="" v-model:model-value="inputString" @click:clear="clearString"
                    @update:model-value="fieldUpdate"></v-text-field>
            </v-col>
            <v-col cols="4">
                <v-btn color="primary" @click="submit">Computing</v-btn>
            </v-col>
        </v-row>


        <v-table height="30em" hover fixed-header style="margin: 0px 0px 20px 0px ;">

            <thead>
                <tr>
                    <th>No.</th>
                    <th>Symbol</th>
                    <th>Probability</th>
                    <th>Floor</th>
                    <th>Ceiling</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="( value, index ) in data[0] ">
                    <td>{{ index + 1 }}</td>
                    <td>{{ value["symbol"] }}</td>
                    <td>{{ value["probility"] * 100 }}%</td>
                    <td>{{ value["floor"] }}</td>
                    <td>{{ value["ceiling"] }}</td>
                </tr>
            </tbody>

        </v-table>

    </v-container>
</template>
<style lang="scss" scoped>
.row {
    &:nth-child(1) {
        border-top: 0px solid #000000;
    }

    &:nth-last-child(1) {
        border-top: 0px solid #000000;
        border-bottom: 1px solid #000000;
    }

    border-top: 1px solid #000000;

    &.symbol {
        background-color: rgb(191, 255, 255);
    }
}
</style>