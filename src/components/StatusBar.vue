<template>
  <h3>Генерируем ваш файл</h3>
  <div class="progress w-100 my-3">
    <div
      class="progress-bar"
      role="progressbar"
      :aria-sort="totalCount > 0 ? (current / totalCount) * 100 : 0"
      aria-valuemin="0"
      aria-valuemax="100"
      :style="{
        width: totalCount > 0 ? `${(current / totalCount) * 100}%` : 0,
      }"
    ></div>
  </div>
  <div class="progress-counter">
    <p>{{ current + "/" + totalCount }}</p>
  </div>
</template>

<script>
export default {
  name: "StatusBar",
  props: {
    current: {
      Number,
      default: 0,
    },
    totalCount: {
      Number,
      default: 0,
    },
  },
};
</script>

<style>
</style>