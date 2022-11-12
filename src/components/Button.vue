<script setup>

defineProps({ text: String, isDisabled: Boolean });

</script>

<template>
    <button :disabled="isDisabled">{{text}}</button>
</template>

<style scoped>

button {
    font-family: 'Lato', sans-serif;
    font-size: 16px;
    padding: 10px 20px;
    border-radius: 6px;
    border: transparent;
    cursor: pointer;
}

button:hover:enabled {
    color: white;
    background-color: #ff2c1f;
}

button:disabled {
    cursor: not-allowed;
    color: gray;
}

</style>
