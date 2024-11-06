<script setup lang="ts">
const text = ref("");
console.log(text.value);
</script>
<template>
  <div class="flex items-center rounded px-4 py-2 bg-white text-gray-700">
    <input v-model="text" placeholder="Search" class="w-full outline-none" />
  </div>
</template>
