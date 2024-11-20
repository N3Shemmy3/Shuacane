<script setup>
const props = defineProps({
  cloudy: {
    type: Boolean,
    default: true,
  },
  sky: {
    validator(value, props) {
      // The value must match one of these strings
      return ["sunny", "rainy", "misty"].includes(value);
    },
    default: "sunny",
  },
});
const styles = reactive({
  sunny: props.sky == "sunny",
  rainy: props.sky == "rainy",
  misty: props.sky == "misty",
});

const cloudy = ref(props.cloudy || props);
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
.clear {
  @apply bg-blue-300  text-[#323232];
}
.rainy {
  @apply bg-slate-400 text-[#323232];
}
.misty {
  @apply bg-gray-100  text-[#323232];
}
.cloudy {
  background-image: url("/public/clouds-bg.png");
  background-position: 60%;
  background-size: cover;
}
</style>
