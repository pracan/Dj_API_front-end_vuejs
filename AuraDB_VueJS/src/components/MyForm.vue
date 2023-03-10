<template>
    <div>
        <p>{{ name }}</p>
        <input ref="el" type="text" placeholder="Name" v-model="name"/>
        <div>
            <!-- <button v-on:click="submitForm"> Submit </button> -->
            <button 
                @click="submitForm"
                v-bind:disabled="name.length==0"
            > Submit </button>
        </div>
    </div>
</template>

<script>
import {ref, onMounted} from 'vue'

export default{
    setup(){
        const name = ref('')
        const el = ref()

        const submitForm = () => {
            console.log(`Form submitted! Name = ${name.value}`)
        }

        //console.log(el.value)
        onMounted(() =>{
            /*console.log(el.value) */
            el.value.focus()
        })

        return {
            el,
            name,
            submitForm,
        }
    },
}
</script>

<style>
input[type='text']{
    padding: 5px
}

button {
    margin-top: 20px;
}
</style>