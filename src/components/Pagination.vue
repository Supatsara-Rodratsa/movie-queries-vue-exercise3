<script setup>
import Button from './Button.vue'

defineProps({ currentPage: Number, totalPage: Number });
const emit = defineEmits(['forwardToPrevPage', 'forwardToNextPage', 'forwardToFirstPage', 'forwardToLastPage']);

function onPrevButtonClicked(currentPage) {
  const prevPage = currentPage - 1;
  if (prevPage > 0) {
    currentPage = prevPage;
    emit('forwardToPrevPage', currentPage);
  }
}

function onNextButtonClicked(currentPage, totalPage) {
    const nextPage = currentPage + 1;
    if (nextPage <= totalPage) {
        emit('forwardToNextPage', nextPage);
    }
}

function updateToFirstPage() {
    emit('forwardToFirstPage', 1);
}

function updateToLastPage(totalPage) {
    emit('forwardToLastPage', totalPage);
}

</script>

<template>
    <div class="flex-row center pagination">
        <Button :text="'<<'" :isDisabled="currentPage == 1" @click="updateToFirstPage()"></Button>
        <Button :text="'Prev'" :isDisabled="currentPage == 1" @click="onPrevButtonClicked(currentPage)"></Button>
        <p>{{currentPage}} of {{totalPage}}</p>
        <Button :text="'Next'" :isDisabled="currentPage == totalPage" @click="onNextButtonClicked(currentPage, totalPage)"></Button>
        <Button :text="'>>'" :isDisabled="currentPage == totalPage" @click="updateToLastPage(totalPage)"></Button>
    </div>
</template>

<style scoped>

.pagination {
    gap: 10px;
    margin: 40px 0;
}

.pagination p {
    font-size: 18px;
}
</style>
