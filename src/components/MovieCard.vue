<script setup>
import StarRating from "./StarRating.vue";

defineProps({ movie: Object });
</script>

<template>
    <div class="card" :style="`background-image: url(${movie.picture})`">
        <div class="not-found-img" v-show="!movie.picture">
            <img src="../assets/movie.png">
            <p>Image Not Found</p>
        </div>
        <div class="content-bg flex-end">
            <div class="content-details flex-col">
                <div class="flex-col">
                    <p class="title">{{movie.title}}</p>
                    <p class="detail">{{movie.genre}} | {{movie.year}} </p>
                </div>
                <div class="flex-row space-between">
                    <div class="flex-row rating">
                        <StarRating :score="Number(movie.score)"></StarRating>
                    </div>
                    <p>Score: {{movie.score}} / 100</p>
                </div>
            </div>
        </div>
    </div>
</template>

<style scoped>
.detail {
    text-transform: uppercase;
    margin-block: 5px;
}

.card {
    height: 100%;
    width: 100%;
    background-repeat: no-repeat;
    background-size: cover;
    min-height: 350px;
    position: relative;
}

.content-bg {
    height: 100%;
    width: 100%;
    background-image: linear-gradient(rgba(0, 0, 0, 0), rgba(0, 0, 0, 0.1), rgba(0, 0, 0, 0.8), rgba(0, 0, 0, 1));
}

.content-details {
    width: 100%;
    padding: 15px;
}

.flex-end img {
    width: 45%;
}

.title {
    font-size: 20px;
    margin-bottom: 5px;
    max-height: 70px;
    display: flex;
    align-items: end;
}

img {
    width: 40%;
}

.not-found-img {
    height: 80%;
    width: 100%;
    text-transform: uppercase;
    position: absolute;
    display: flex;
    flex-direction: column;
    justify-content: center;
    align-items: center;
}

.not-found-img p {
    font-weight: 700;
    margin: 10px 0;
    color: #4d4d4d;
}
</style>
