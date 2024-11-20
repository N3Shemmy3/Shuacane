<template>
  <!-- Fallback when no data is loaded -->
  <div
    class="fixed z-[100] bg-white left-0 top-0 right-0 bottom-0 overflow-clip flex p-8 flex-col items-center justify-center space-y-2"
  >
    <Icon name="ic:outline-wifi-off" class="size-[144px] md:size-[160px]" />
    <h3 class="text-xl md:text-2xl">You're offline</h3>
    <p class="text-sm md:text-base text-pretty text-center">
      Check your internet connection and try again.
    </p>

    <button
      @click="reload()"
      class="w-fit flex items-center justify-center px-4 py-2 rounded-full aspect-video transition-all duration-300 bg-blue-500 text-white cursor-pointer hover:bg-opacity-50"
    >
      Retry
    </button>
  </div>
</template>
<script setup>
function reload() {
  window.location.reload();
}
</script>
