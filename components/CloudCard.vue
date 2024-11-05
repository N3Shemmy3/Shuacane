<template>
  <div class="h-fit relative rounded-md">
    <img
      src="/public/clouds-bg.png"
      class="relative z-[-1] w-fit h-full object-cover"
    />
    <div class="top-0 left-0 right-0 bottom-0 absolute z-[1] px-4 py-2">
      <slot />
    </div>
  </div>
</template>
