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
const hourlyData = ref([]);
const city = ref("Solwezi");
const currentTime = ref(null);
const loading = ref(false);
const error = ref(null);

// Function to fetch current weather data
const fetchWeather = async (city) => {
  fetchForecast(city);
  fetchHourlyData(city);
  const url = `${baseUrl.value}${endPoints.value.current}?key=${apiKey.value}&q=${city}`;
  loading.value = true;
  error.value = null;

  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`Error: ${response.statusText}`);
    }

    // Store the response data
    weatherData.value = await response.json();

    // Extract and format the time
    if (weatherData.value.location && weatherData.value.location.localtime) {
      const time = new Date(weatherData.value.location.localtime);
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
const fetchForecast = async (city, days = 7) => {
  const url = `${baseUrl.value}${endPoints.value.forecast}?key=${apiKey.value}&q=${city}&days=${days}`;
  loading.value = true;
  error.value = null;

  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`Error: ${response.statusText}`);
    }
    forecastData.value = await response.json();
    // console.log(forecastData.value);
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};
const fetchHourlyData = async (city) => {
  const url = `${baseUrl.value}${endPoints.value.forecast}?key=${apiKey.value}&q=${city}&days=1`; // Fetch forecast for 1 day
  loading.value = true;
  error.value = null;

  try {
    const response = await fetch(url);
    if (!response.ok) {
      throw new Error(`Error: ${response.statusText}`);
    }
    const data = await response.json();

    // Extract hourly data for the current day
    if (data.forecast && data.forecast.forecastday.length > 0) {
      const hourlyDataArray = data.forecast.forecastday[0].hour;

      // Get the current hour in the user's local timezone
      const currentTime = new Date();
      const currentHour = currentTime.getHours(); // Get current hour

      // Find the index of the current hour in the hourly data
      const startIndex = hourlyDataArray.findIndex((hour) => {
        const hourTime = new Date(hour.time).getHours();
        return hourTime === currentHour; // Compare the hour
      });

      // Slice the array starting from the current time
      hourlyData.value = hourlyDataArray.slice(startIndex);
      console.log(hourlyData.value); // Debug: Log the filtered hourly data
    }
  } catch (err) {
    error.value = err.message;
  } finally {
    loading.value = false;
  }
};

// get cions based on the condition
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
const getSkyCondition = (condition) => {
  let state = "sunny";
  if (condition.toLocaleLowerCase().includes(state)) {
    state = "sunny";
  } else {
    state = "rainy";
  }
  return state;
};
onMounted(() => {
  fetchWeather(city.value);
});
</script>
<template>
  <div>
    <!-- Loading State -->
    <div
      v-if="loading"
      class="fixed z-[100] bg-white left-0 top-0 right-0 bottom-0 overflow-clip flex p-8 flex-col items-center justify-center space-y-2"
    >
      Loading...
    </div>

    <Container
      v-else-if="weatherData && weatherData.location && weatherData"
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
        <CloudCard
          :cloudy="weatherData.current.condition.text.includes('cloudy')"
          :sky="getSkyCondition(weatherData.current.condition.text)"
        >
          <div class="md:w-full p-4 space-y-8">
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
              <h2 class="text-7xl md:text-8xl">
                {{ weatherData.current.temp_c }}°<sup>c</sup>
              </h2>
              <h6>{{ weatherData.current.condition.text }}</h6>
            </div>
            <!-- Specifics -->
            <div
              class="flex items-center justify-between *:flex *:items-center *:space-x-2 text-sm ,md:text-base text-nowrap"
            >
              <!-- Pressure -->
              <span>
                <Icon name="solar:water-line-duotone" />
                <span> {{ weatherData.current.pressure_mb }} hPa </span>
              </span>

              <!-- Humidity -->
              <span>
                <Icon name="solar:waterdrop-outline" />
                <span> {{ weatherData.current.humidity }}% </span>
              </span>

              <!-- Wind Speed -->
              <span>
                <Icon name="solar:wind-outline" />
                <span> {{ weatherData.current.wind_kph }} km/h </span>
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

      <section class="-mx-4 md:mx-0 space-y-8 md:w-80">
        <!--hourly focast-->
        <div>
          <span class="mx-4 text-base">Today</span>

          <ul
            class="flex gap-3 px-4 py-2 overflow-x-scroll overflow-clip snap-x snap-mandatory overscroll-contain scroll-smooth"
          >
            <li
              v-for="(hour, index) in hourlyData"
              :key="index"
              class="flex flex-col items-center justify-center space-y-2 px-4 py-2 rounded"
              :class="{ 'bg-[#ffd89e]': index == 0 }"
            >
              <span v-if="index == 0" class="text-base text-nowrap">Now </span>
              <span v-else class="text-base text-nowrap">
                {{
                  new Date(hour.time).toLocaleTimeString("en-US", {
                    hour: "numeric",
                    hour12: true,
                  })
                }}
              </span>
              <img
                class="size-[32px] object-cover"
                :src="hour.condition.icon"
                :alt="hour.condition.text"
              />

              <span class="text-base">{{ hour.temp_c }}°C</span>
            </li>
          </ul>
        </div>
        <div class="space-y-2">
          <span class="mx-4 text-base">7-Day forcast</span>
          <!--Daily focast loist-->
          <ul class="flex flex-col">
            <li
              v-for="(day, index) in forecastData.forecast.forecastday"
              :key="index"
              class="flex items-center px-4 py-2"
            >
              <div class="w-full flex flex-col">
                <span class="text-lg font-bold">{{
                  new Date(day.date).toLocaleDateString("en-US", {
                    weekday: "long",
                  })
                }}</span>
                <span class="text-base opacity-60"
                  >{{
                    new Date(day.date).toLocaleDateString("en-US", {
                      day: "numeric",
                    })
                  }}
                  {{
                    new Date(day.date).toLocaleDateString("en-US", {
                      month: "short",
                    })
                  }}</span
                >
              </div>
              <div class="w-full flex items-center justify-between">
                <h4 class="text-lg">{{ day.day.maxtemp_c }}°</h4>
                <img
                  class="size-[48px] object-cover"
                  :src="day.day.condition.icon"
                  :alt="day.day.condition.text"
                />
              </div>
            </li>
          </ul>
        </div>
      </section>
    </Container>
    <!-- Fallback when no data is loaded -->
    <div
      class="fixed z-[100] bg-white left-0 top-0 right-0 bottom-0 overflow-clip flex p-8 flex-col items-center justify-center space-y-2"
      v-else
    >
      <Icon name="ic:outline-wifi-off" class="size-[144px] md:size-[160px]" />
      <h3 class="text-xl md:text-2xl">You're offline</h3>
      <p class="text-sm md:text-base text-pretty text-center">
        Check your internet connection and try again.
      </p>

      <button
        @click="fetchWeather(city)"
        class="w-fit flex items-center justify-center px-4 py-2 rounded-full aspect-video transition-all duration-300 bg-blue-500 text-white cursor-pointer hover:bg-opacity-50"
      >
        Retry
      </button>
    </div>
    <Footer class="mt-auto align-bottom" />
  </div>
</template>
<style>
a {
  @apply w-fit h-fit transition-all duration-300 text-blue-800 hover:text-blue-300;
}
</style>
