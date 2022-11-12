<script setup>
defineProps({ text: String });

</script>

<template>
    <div class="flex-col center blank-container">
        <i class="fa fa-search"></i>
        <p>{{text || 'Data Not Found'}}</p>
    </div>
</template>

<style scoped>

.blank-container {
    height: 200px;
    margin: 0 30px 30px 30px;
    background-color: #1c1c1c;;
}

.fa {
    color: white;
    font-size: 28px;
    margin: 10px 0;
}

p {
    text-transform: uppercase;
    font-size: 16px;
    font-weight: 500;
    color: white;
}
</style>
