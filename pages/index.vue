<script setup>
const apiKey = ref("35f16ed8867e4e7a96973224240711");
const baseUrl = ref("http://api.weatherapi.com");
const endPoints = ref({
  current: "/v1/current.json",
  forecast: "/v1/forecast.json",
  search: "/v1/search.json",
  history: "/v1/history.json",
  astronomy: "/v1/astronomy.json",
  timeZone: "/v1/timezone.json",
  sports: "/v1/sports.json",
  ipLookup: "/v1/ip.json",
  airQuality: "/v1/airquality.json",
});

// Ref to store API response
const weatherData = ref(null);
const forecastData = ref(null);
const city = ref("London");
const currentTime = ref(null);
const loading = ref(false);
const error = ref(null);

// Function to fetch current weather data
const fetchWeather = async (city) => {
  const url = `${baseUrl.value}${endPoints.value.current}?key=${apiKey.value}&q=${city}`;
  loading.value = true;
  error.value = null;

  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`Error: ${response.statusText}`);
    }
    weatherData.value = await response.json();
    console.log(weatherData.value);
    // Extract and format the time
    if (data.location && data.location.localtime) {
      const time = new Date(data.location.localtime);
      currentTime.value = time.toLocaleString("en-US", {
        hour: "numeric",
        minute: "numeric",
        hour12: true,
      });
    }
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};
// Function to fetch weather forecast data
const fetchForecast = async (city, days = 6) => {
  const url = `${baseUrl.value}${endPoints.value.forecast}?key=${apiKey.value}&q=${city}&days=${days}`;
  loading.value = true;
  error.value = null;

  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`Error: ${response.statusText}`);
    }
    forecastData.value = await response.json();
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};
//
const getConditionIcon = (condition) => {
  let icon = "";
  switch (condition) {
    case "Clear":
      icon = "meteocons:clear-day";
      break;
    case "Sunny":
      icon = "meteocons:clear-day-fill";
      break;
    case "Partly cloudy":
      icon = "meteocons:partly-cloudy-day-fill";
      break;
    case "Rain":
      icon = "meteocons:rain-fill";
      break;
  }
  return icon;
};
onMounted(() => {
  fetchWeather(city.value);
  fetchForecast(city.value);
});
</script>
<template>
  <div>
    <!-- Loading State -->
    <div v-if="loading">Loading...</div>

    <!-- Error State -->
    <Container
      v-else-if="weatherData && weatherData.location && weatherData.current"
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
                <h4 class="text-lg font-bold">
                  {{ weatherData.location.name }}
                </h4>
              </div>
              <div class="flex space-x-2 items-center">
                <h5>{{ currentTime }}</h5>
              </div>
            </div>

            <!--Current Weather-->
            <div
              class="flex flex-col items-center justify-center mt-10S mb-6 space-y-2"
            >
              <h2 class="text-8xl">{{ weatherData.current.temp_c }}</h2>
              <h6>{{ weatherData.current.condition.text }}</h6>
            </div>
            <!--Specifics-->
            <div
              class="flex items-center justify-between *:flex *:items-center *:space-x-2 text-base"
            >
              <span>
                <Icon name="solar:water-line-duotone" />
                <span>
                  {{ weatherData.current.condition.pressure_mb }} hpa
                </span>
              </span>
              <span>
                <Icon name="solar:waterdrop-outline" />
                <span> {{ weatherData.current.condition.humidity }}% </span>
              </span>
              <span>
                <Icon name="solar:wind-outline" />
                <span> {{ weatherData.current.condition.wind_kph }}km/h </span>
              </span>
            </div>
          </div>
          <!--Temp Div-->
          <div
            class="relative md:w-full px-4 py-2 m-2 space-y-2 bg-white bg-opacity-40 backdrop-blur rounded"
          >
            <h6 class="text-sm font-bold">Tempreture</h6>
            <ul class="flex justify-between items-center">
              <li
                v-for="n in 4"
                class="flex flex-col items-center text-center px-2 py-1 rounded"
              >
                <Icon name="meteocons:clear-day-fill" />
                <span class="text-[12px]"> Morning </span>
                <span>26°</span>
              </li>
            </ul>
          </div>
        </CloudCard>
        <ul class="mt-8 grid gap-8 grid-cols-2">
          <li
            v-for="n in 4"
            class="flex w-full rounded min-h-[150px] p-4 bg-blue-200"
          >
            <!--Info side-->
            <div class="w-full">
              <h4 class="text-lg">Wind</h4>
            </div>
            <!--Icon side-->
            <div></div>
          </li>
        </ul>
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
              v-for="(day, index) in forecastData.forecast.forecastday"
              :key="index"
              class="flex flex-col items-center justify-center px-4 py-2 rounded"
              :class="{ 'bg-[#ffd89e]': index == 0 }"
            >
              <span class="text-sm">
                {{
                  new Date(day.date).toLocaleDateString("en-US", {
                    weekday: "long",
                    month: "short",
                    day: "numeric",
                  })
                }}
              </span>
              <Icon :name="getConditionIcon(day.day.condition.text)" />
              <span class="text-base">{{ day.day.mintemp_c }}°</span>
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
    <!-- Fallback when no data is loaded -->
    <div
      class="w-full h-screen overflow-clip flex p-8 flex-col items-center justify-center space-y-2"
      v-else
    >
      <Icon name="ic:outline-wifi-off" class="size-[144px] md:size-[160px]" />
      <h3 class="text-xl md:text-2xl">You're offline</h3>
      <p class="text-sm md:text-base text-pretty text-center">
        Check your internet connection and try again.
      </p>

      <button
        class="w-fit flex items-center justify-center px-4 py-2 rounded-full aspect-video transition-all duration-300 bg-blue-500 text-white cursor-pointer hover:bg-opacity-50"
      >
        Retry
      </button>
    </div>
  </div>
</template>
