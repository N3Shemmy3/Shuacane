<script setup>
const props = defineProps(["size"]);
const size = "0 0 " + props.size + " " + props.size;
</script>

<template>
  <svg
    class="progress-circle"
    viewBox="0 0 70 70"
    xmlns="http://www.w3.org/2000/svg"
  >
    <!-- Background circle -->
    <circle
      class="progress-circle-bg"
      cx="35"
      cy="35"
      r="20"
      fill="none"
      stroke="rgba(0, 0, 0, 0.1)"
      stroke-width="4"
    ></circle>
    <!-- Animated spinning arc -->
    <circle
      class="progress-circle-arc"
      cx="35"
      cy="35"
      r="20"
      fill="none"
      stroke="currentColor"
      stroke-width="4"
      stroke-linecap="round"
      stroke-dasharray="125.6"
    ></circle>
  </svg>
</template>
<style>
/* Size of the spinner */
.progress-circle {
  animation: rotate 2s linear infinite; /* Smooth rotation */
}

.progress-circle-arc {
  stroke-dasharray: 125.6; /* Total circumference */
  stroke-dashoffset: 0;
  animation: dash 1.5s ease-in-out infinite;
  transform-origin: center;
  transform: rotate(-90deg); /* Start from top */
}

/* Smooth continuous rotation */
@keyframes rotate {
  0% {
    transform: rotate(0deg);
  }
  100% {
    transform: rotate(360deg);
  }
}

/* Smooth dash transitions */
@keyframes dash {
  0% {
    stroke-dashoffset: 125.6; /* Circle fully hidden */
  }
  50% {
    stroke-dashoffset: 31.4; /* Partial arc visible */
  }
  100% {
    stroke-dashoffset: 125.6; /* Circle hidden again */
  }
}
</style>
