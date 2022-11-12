<script setup>

defineProps({ lists: Array, currentSelectedItems: Array });
const emit = defineEmits(["updateSelectedItems"]);

function onSelectedItems(item) {
  emit("updateSelectedItems", item);
}
</script>

<template>
  <select
    v-model="currentSelectedItems"
    multiple
    @change="onSelectedItems(currentSelectedItems)"
  >
    <template v-for="list in lists" v-bind:key="list">
      <option>{{ list }}</option>
    </template>
  </select>
</template>

<style scoped>
select {
  border-radius: 10px;
}

select:focus {
  outline: none;
}

select option {
  height: 30px;
  display: flex;
  align-items: center;
  padding: 5px 20px;
  min-width: 125px;
  background: black;
  color: white;
  font-size: 12px;
}

select option:checked, select[multiple]:focus option:checked {
  background: lightgray linear-gradient(0deg, lightgray 0%, lightgray 100%) !important;
  color: black;

}
</style>
