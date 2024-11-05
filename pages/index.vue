<template>
  <Container class="space-y-6">
    <!--Cloudy or Sunny Card-->
    <CloudCard>
      <div class="p-4 space-y-4">
        <div class="flex items-center space-x-2 justify-between">
          <div class="flex space-x-2 items-center">
            <Icon name="solar:map-point-outline" />
            <h4 class="text-lg font-bold">Gotham</h4>
          </div>
          <div class="flex space-x-2 items-center">
            <h5>Today 5pm</h5>
          </div>
        </div>

        <!--Current Weather-->
        <div
          class="flex flex-col items-center justify-center mt-10S mb-6 space-y-2"
        >
          <h2 class="text-8xl">23°</h2>
          <h6>Mostly Clear</h6>
        </div>
        <!--Specifics-->
        <div
          class="flex items-center justify-between *:flex *:items-center *:space-x-2 text-base"
        >
          <span>
            <Icon name="solar:water-line-duotone" />
            <span> 720hpa </span>
          </span>
          <span>
            <Icon name="solar:waterdrop-outline" />
            <span> 32% </span>
          </span>
          <span>
            <Icon name="solar:wind-outline" />
            <span> 12km/h </span>
          </span>
        </div>
      </div>
      <!--Temp Div-->
      <div
        class="box-border px-4 py-2 m-2 space-y-2 bg-white bg-opacity-40 backdrop-blur rounded"
      >
        <h6 class="text-sm font-bold">Tempreture</h6>
        <div class="flex justify-between items-center">
          <div
            v-for="n in 4"
            class="flex flex-col items-center text-center px-2 py-1 rounded"
          >
            <Icon name="meteocons:clear-day-fill" />
            <span class="text-[12px]"> Morning </span>
            <span>26°</span>
          </div>
        </div>
      </div>
    </CloudCard>
    <div class="-mx-4">
      <!--Daily focast-->
      <div class="space-y-2">
        <span class="mx-4">Today</span>

        <!--Daily focast grid-->
        <div
          class="flex gap-3 px-4 overflow-x-scroll overflow-clip overscroll-contain scroll-smooth"
        >
          <div
            v-for="(n, index) in 10"
            class="flex flex-col items-center justify-center px-4 py-2 rounded"
            :class="{ 'bg-[#ffd89e]': index == 0 }"
          >
            <span class="text-sm"> Now </span>
            <Icon name="meteocons:clear-day-fill" />
            <span class="text-base">26°</span>
          </div>
        </div>
      </div>
    </div>
  </Container>
</template>
