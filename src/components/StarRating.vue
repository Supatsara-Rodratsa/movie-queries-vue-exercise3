<script setup>
const props = defineProps({ score: Number });

function renderRating(score) {
  let starItem = [];
  let totalScore = (score * 5) / 100;
  let fullScore = 5;

  while (fullScore > 0) {
    if (Math.trunc(totalScore) > 0) {
      starItem.push('<i class="fa fa-star">');
    } else if (totalScore >= 0.5) {
      starItem.push('<i class="fa fa-star-half-o">');
    } else {
      starItem.push('<i class="fa fa-star-o">');
    }
    totalScore -= 1;
    fullScore--;
  }

  return starItem;
}
</script>

<template>
  <div v-for="item in renderRating(score)" :key="item">
    <div v-html="item" />
  </div>
</template>

<style scoped>
.switch {
  position: relative;
  display: inline-block;
  width: 110px;
  height: 34px;
  margin: 20px;
}

.switch input {
  display: none;
}

.slider {
  position: absolute;
  cursor: pointer;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: #1f1f1f;
  transition: 0.4s;
  box-shadow: rgba(0, 0, 0, 0.25) 0px 54px 55px,
    rgba(0, 0, 0, 0.12) 0px -12px 30px, rgba(0, 0, 0, 0.12) 0px 4px 6px,
    rgba(0, 0, 0, 0.17) 0px 12px 13px, rgba(0, 0, 0, 0.09) 0px -3px 5px;
}

.slider:before {
  position: absolute;
  content: "";
  height: 26px;
  width: 26px;
  left: 4px;
  bottom: 4px;
  background-color: white;
  transition: 0.4s;
}

input:checked + .slider {
  background-color: #ff2c1f;
}

input:checked + .slider:before {
  transform: translateX(75px);
}

.watched {
  display: none;
}

.watched,
.un-watched {
  color: white;
  position: absolute;
  transform: translate(-50%, -50%);
  top: 50%;
  left: 50%;
  font-size: 12px;
  width: 100%;
  margin-left: 37px;
  font-weight: 500;
}

input:checked + .slider .watched {
  display: block;
  text-align: start;
  margin-left: 20px !important;
}

input:checked + .slider .un-watched {
  display: none;
}

.slider.round {
  border-radius: 34px;
}

.slider.round:before {
  border-radius: 50%;
}

</style>
