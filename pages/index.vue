<template>
  <Container
    class="space-y-8 md:space-y-0 scroll-smooth md:flex-row 2xl:border 2xl:rounded overflow-clip *:p-4"
  >
    <!--menu sidebar-->
    <div class="md:w-80 md:p-0">
      <SearchBar />
      <hr />
      <ul></ul>
    </div>
    <div class="md:w-full space-y-4 md:border-x">
      <!--Cloudy or Sunny Card-->
      <CloudCard>
        <div class="md:w-full p-4 space-y-4">
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
          class="relative md:w-full px-4 py-2 m-2 space-y-2 bg-white bg-opacity-40 backdrop-blur rounded"
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
      <div class="mt-8 grid gap-8 grid-cols-2">
        <div
          v-for="n in 4"
          class="flex w-full rounded min-h-[150px] p-4 bg-blue-200"
        >
          <!--Info side-->
          <div class="w-full">
            <h4 class="text-lg">Wind</h4>
          </div>
          <!--Icon side-->
          <div></div>
        </div>
      </div>
    </div>
    <!--Desktop sidebar-->

    <!--Daily focast-->
    <section class="-mx-4 md:mx-0 space-y-8 md:w-80">
      <!--hourly focast-->
      <div>
        <span class="mx-4 text-base">Today</span>

        <ul
          class="flex gap-3 px-4 py-2 overflow-x-scroll overflow-clip overscroll-contain scroll-smooth"
        >
          <li
            v-for="(n, index) in 10"
            class="flex flex-col items-center justify-center px-4 py-2 rounded"
            :class="{ 'bg-[#ffd89e]': index == 0 }"
          >
            <span class="text-sm"> Now </span>
            <Icon name="meteocons:clear-day-fill" />
            <span class="text-base">26°</span>
          </li>
        </ul>
      </div>
      <div class="space-y-2">
        <span class="mx-4 text-lg">This week</span>
        <!--Daily focast grid-->
        <ul class="flex flex-col">
          <li v-for="n in 6" class="flex items-center px-4 py-2">
            <div class="w-full flex flex-col">
              <span class="text-base">Tommorow</span>
              <span class="text-base opacity-40">{{ n + 10 }} Aprl</span>
            </div>
            <div class="w-fit flex items-center justify-between">
              <h4 class="text-lg">26°</h4>
              <Icon size="48" name="meteocons:clear-day-fill" />
            </div>
          </li>
        </ul>
      </div>
    </section>
  </Container>
</template>
