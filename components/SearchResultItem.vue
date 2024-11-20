<script setup>
const props = defineProps({
  city: {
    type: { name: String, region: String, country: String },
    required: true,
  },
});
const onEndIconClicked = () => {
  emit("onEndIconClicked");
};
</script>

<template>
  <li
    class="flex box-border items-center px-4 py-1 transition-colors duration-300 hover:bg-gray-100 cursor-pointer"
  >
    <Icon name="ic:outline-location-on" size="24" />
    <div class="ml-2 mr-auto">
      <h6 class="text-base">{{ props.city.name }}</h6>
      <p class="text-sm opacity-70">{{ props.city.country }}</p>
    </div>
    <button class="p-2" @click="onEndIconClicked">
      <Icon name="ic:outline-bookmark-border" size="22" />
    </button>
  </li>
</template>
