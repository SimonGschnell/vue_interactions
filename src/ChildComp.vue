<script setup>
import {ref} from 'vue'
const childComponentText =ref("this is a child component")
const message = 'hello world'

// we can receive parameters from the parent component by using the macro defineProps
const props = defineProps
({
    msg:String,
    text:String
})

// we can respond to the parent component by emitting messages 
const emit = defineEmits(["response"])
//emit('response','hello world') -> the first parameter is which event is triggered when emitting
//and the second parameter is the message we are returning to the parent component

</script>

<template>
    <p style="color:green">{{ childComponentText }} - {{ text }}</p>
    <p style="color:blue">{{ msg }}</p>
    <button @click="emit('response',message)">click me to emit: {{ message }} to parent component</button>
</template>