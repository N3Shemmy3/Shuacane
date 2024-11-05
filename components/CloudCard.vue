<script setup>
const props = defineProps({
  cloudy: {
    type: Boolean,
    default: true,
  },
  sky: {
    validator(value, props) {
      // The value must match one of these strings
      return ["sunny", "rainy"].includes(value);
    },
    default: "sunny",
  },
});
const styles = reactive({
  sunny: props.sky == "sunny",
  rainy: props.sky == "rainy",
});
</script>

<template>
  <div class="rounded-md shadow-sm" :class="styles">
    <div
      class="box-content"
      :class="{
        cloudy: props.cloudy,
      }"
    >
      <slot />
    </div>
  </div>
</template>
<style>
.sunny {
  background-color: #ffd89e;
  color: black;
}
.rainy {
  @apply bg-slate-500 dark:bg-slate-700 text-[#fafafa];
}
.cloudy {
  background: url("/public/clouds-bg.png");
  background-position: center;
  background-size: cover;
}
</style>
