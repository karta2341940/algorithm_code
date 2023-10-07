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

        
            <v-table height="20em" hover="true" fixed-header="true">

                <thead>
                    <tr>
                        <th>Symbol</th>
                        <th>Probability</th>
                    </tr>
                </thead>
                <tbody>
                    <tr v-for="( symbol, index ) in  processedString ">
                        <td>{{ symbol }}</td>
                        <td>{{ String(probilities[index]*100).substring(0,8) }}%</td>
                    </tr>
                </tbody>

            </v-table>
     




    </v-container>
</template>

<script setup>
import { ref } from 'vue'
const inputString = ref('')
const processedString = ref()
const probilities = ref()

function clearString() {
    inputString.value = ''
    processedString.value = []
    probilities.value = []
}
function submit() {
    console.log("Submit")
    
}
function fieldUpdate() {
    try {
        processedString.value = inputString.value.split('').map((value) => value === " " ? "▲" : value).filter((item, index, array) => array.indexOf(item) === index)
        probilities.value = inputString.value.split('').map((item, index, array) => {
            return array.filter((item2) => item2 === item).length / array.length
        })
    }
    catch (e) {
        inputString.value = ''
        processedString.value = []
        probilities.value = []
    }
}
</script>
