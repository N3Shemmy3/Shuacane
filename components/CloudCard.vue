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
  <div class="border-4 border-transparent rounded-md shadow-sm" :class="styles">
    <div
      class="md:flex md:flex-row md:columns-2"
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
  background-position: 60%;
  background-size: cover;
}
</style>
