<script setup lang="ts">
const text = ref("");
console.log(text.value);
</script>
<template>
  <div class="flex items-center rounded px-4 py-2 bg-white text-gray-700">
    <input v-model="text" placeholder="Search" class="w-full outline-none" />
    <button
      class="size-12 aspect-square flex items-center justify-center rounded-full bg-blue-600"
    >
      <Icon name="ic:outline-gps-fixed" />
    </button>
  </div>
</template>
