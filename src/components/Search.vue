<script setup>
import { watch } from "@vue/runtime-core";
import { ref } from 'vue';

defineProps({ placeholder: String, isNumberOnly: Boolean, hideButton: Boolean });
const search = ref('');
const emit = defineEmits(['onSearchButtonClicked']);

function onSearchButtonClicked(value) {
    emit('onSearchButtonClicked', value);
} 

watch(search, newSearch => {
    onSearchButtonClicked(newSearch)
});

</script>

<template>
  <div class="search-container">
    <input v-on:keyup.enter="onSearchButtonClicked(search)" :type="isNumberOnly ? 'number':'text'" :placeholder="placeholder || 'Search..'" v-model="search">
    <button v-show="!hideButton" @click="onSearchButtonClicked(search)"><i class="fa fa-search"></i></button>
  </div>
</template>

<style scoped>
.search-container {
    margin: 20px auto 50px auto;
    position: relative;
    width: 350px;
}

input {
    padding: 10px 20px;
    border: transparent;
    border-radius: 20px;
    width: 320px;
    font-size: 16px;
    font-family: 'Lato', sans-serif;
  }

input:focus{
    outline: none;
}

button {
    border-radius: 50%;
    width: 35px;
    height: 35px;
    padding: 0px;
    position: absolute;
    right: -6px;
    top: 2px;
    background-color: #ff2c1f;
    border-color: transparent;
    cursor: pointer;
}

button .fa {
    color: white;
}

</style>
