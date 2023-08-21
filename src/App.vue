<script setup>
//
import ChildComp from './ChildComp.vue'
import {ref, reactive,computed, onMounted,watch} from 'vue'
// --> reactive state in vue is created with either ref() or reactive()
// ref is used for strings and other primitive types 
// reactive is used for more complex objects

//creation of reactive state
const message = ref("some text");
const counter = reactive({
  count: 0
})

//manipulation of reactive state
message.value = "Hello World"
counter.count +=1

//state for class binding
const titleClass = ref("title")

//function for the v-on:click (@click) directive
function increment(){
  counter.count +=1
}

//
const input_value = ref("")
function oninput(e){
  input_value.value = e.target.value
}
const fav_language=ref("HTML")

//state for the list elements rendered with v-for

const elements = ref([{name:"hello",done:false},{name:"world",done:false},{name:"vue",done:false},{name:"is",done:false},{name:"awesome",done:false}])
const elementsInput = ref("")
function removeItem(element){
  elements.value =elements.value.filter((e)=>e.name!= element.name)
}
function addItem(element){
  elements.value.unshift({name:element,done:false})
  
}

// state for showing only unfinished elements or all elements with the computed function
const hide_done = ref(false)
// we can use 2 reactive states to create a state that changes depending on the dependencies
// this approach does not change the original list, this way we don't mutate irreversable 
const filteredElements = computed(()=>{
return hide_done.value?elements.value.filter((e)=>!e.done):elements.value
})

// we can directly access a DOM element by passing a ref to the element
const pElement = ref(null)
// vue uses lifecycle hooks to access elements on different stages of their life cycle
// inside of the onMounted life cycle we can be sure that the component has been mounted on the DOM
onMounted(()=>{
  pElement.value.textContent="Hello Worlds"
})

// we can watch reactive state and react to it with the watch function
// watch function takes the state and a function to execute as parameters
// this is an example where we fetch new data from an API when the todoID increases
const todoData =ref(null)
const todoId = ref(0)
async function fetch_data(){
  todoData.value=null
  let res =  await fetch(`https://jsonplaceholder.typicode.com/todos/${todoId.value}`)
  todoData.value=await res.json()
}
watch(todoId,fetch_data)

</script>

<template>
<!--the mustache syntax injects js into the content of html-->
<h1>{{counter.count}}</h1>
<h1>{{message.split("").reverse().join("") }}</h1>

<!--to inject js to html attributes we use the v-bind directive-->
<!--short hand for the v-bind directive is (:class="titleClass") just leave "v-bind" away-->
<h1 v-bind:class="titleClass">{{message}}</h1> 

<!--to inject js to events we use the v-on directive, example: v-on:click="fun", shorthand: @click="fun"-->
<button :class="'block'" v-on:click="increment">Increase counter :{{counter.count}}</button>

<!--for Form fields we need both v-bind and v-on / v-bind sets the value / v-on will change the value when the user gives input-->
<input v-bind:value="input_value" v-on:input="oninput"/>

<!--short hand form for both v-bind and v-on is: v-model that binds directly to a reactive state-->
<input v-model="input_value"/>
<p>inputs value: {{ input_value }} // radios value: {{ fav_language }}</p>
<!---->
<input type="radio" id="html" v-model="fav_language" value="HTML">
<label for="html">HTML</label><br>
<input type="radio" id="css" v-model="fav_language" value="CSS">
<label for="css">CSS</label><br>
<input type="radio" id="javascript" v-model="fav_language" value="JavaScript">
<label for="javascript">JavaScript</label> 

<!--we can conditionaly render elements with the v-if / v-else-if / v-else directives-->
<!--the directive accepts a truthy or falsy value-->
<p v-if="counter.count<5">counter is less than 5</p>
<p v-else-if="counter.count<10">counter is less than 10</p>
<p v-else>counter is greater than 10</p>

<!--we can loop through objects with the v-for -->
<input v-model="elementsInput" /><button @click="addItem(elementsInput)">ADD</button>
<ul>
  <li v-for="element in filteredElements" v-bind:key="element" >
    <input type="checkbox" v-model="element.done"/>
    <span :class="element.done?'strike':''">{{ element.name }}</span>
    <button @click="removeItem(element)">X</button></li>
</ul>
<button @click="hide_done=!hide_done">{{ hide_done?"show all":"hide completed" }}</button>
<br/><br/>
<!---->
<p>the bellow paragraph was changed by accessing the DOM element with ref and changing the value onMount</p>
<p ref="pElement">some placeholder text</p>
<br/><br/>
<!--the pre element is very useful to print js objects-->
<button @click="todoId++">increase todoId: {{todoId}}</button>
<p v-if="!todoData">is loading...</p>
<pre v-else>{{ todoData }}</pre>
<!-- we can include a child component by importing it with: import ChildComp from './ChildComp.vue'-->
<!-- we can pass props to the Child component by passing the value as an attribute on the element-->
<!-- we can also pass a value from the state if we bind the attribute with the correct name-->
<ChildComp @response="(msg)=>console.log(msg)" text="that received a prop the normal way" msg="this message comes from the parent component"/>
<!-- we can receive messages from the child component by emiting messages from the child-->
<!-- we catch the emitted messages from the cild component by reacting to the event that is emitted-->
<ChildComp @response="(msg)=>console.log(msg)" text="that received a prop by binding the value" :msg="fav_language"/>
</template>

<style>
.title{color:red}
.block{display: block}
.strike{text-decoration: line-through;}
</style>
