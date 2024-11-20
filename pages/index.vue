<script setup>
import L from "leaflet";

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
const isSearchDialogOpen = ref(false);
const weatherData = ref(null);
const forecastData = ref(null);
const hourlyData = ref([]);
const query = ref("Lusaka"); // Search input
const cities = ref([]); // List of cities
const isSearching = ref(false); // Searching state
const currentTime = ref(null);
const loading = ref(false);
const error = ref(null);

// Fetch cities based on search query
const fetchCities = async () => {
  if (query.value.trim() === "") {
    cities.value = [];
    return;
  }

  isSearching.value = true; // Indicate search has started
  try {
    // Construct URL with query parameters
    const url = new URL(baseUrl.value + endPoints.value.search);
    url.searchParams.append("key", apiKey.value);
    url.searchParams.append("q", query.value);

    // Fetch data from API
    const response = await fetch(url);

    if (!response.ok) {
      throw new Error(`HTTP error! Status: ${response.status}`);
    }

    const data = await response.json();
    cities.value = data; // Update cities with API response
  } catch (error) {
    console.error("Error fetching cities:", error);
  } finally {
    isSearching.value = false; // Indicate search has ended
  }
};
// Function to fetch current weather data
const fetchWeather = async (city) => {
  isSearchDialogOpen.value = false;
  isSearching.value = false;
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
    //make map object
    createMap(weatherData.value.location.lon, weatherData.value.location.lat);
    console.log(weatherData.value);
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
  if (query.value.length < 3) return;
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
const fetchHourlyData = async (city) => {
  // Fetch forecast for 1 day
  const url = `${baseUrl.value}${endPoints.value.forecast}?key=${apiKey.value}&q=${city}&days=1`;
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
const createMap = (lon, lat) => {
  // Initialize the map
  const map = L.map("map").setView([lat, lon], 13);

  // Add a tile layer (OpenStreetMap)
  L.tileLayer("https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png", {
    maxZoom: 19,
  }).addTo(map);

  // Add a marker at the city location
  L.marker([lat, lon]).addTo(map).bindPopup("Current City").openPopup();
  console.log(map);
};
// toggle overlay visibility depending on the id
function toggleOverlay(event) {
  const targetId = event.id;
  switch (targetId) {
    case "message-overlay": {
      //hide the other to avoid both being vissible at once
      showSearchView.value = false;
      showDialog.value = !showDialog.value;
    }
    case "search-overlay": {
      //hide the other to avoid both being vissible at once
      showDialog.value = false;
      showSearchView.value = !showSearchView;
    }
  }
}
onMounted(() => {});
const specifics = [
  {
    icon: "meteocons:windsock",
    title: "Wind Speed",
    text: "10km/hr",
  },
  {
    icon: "meteocons:raindrops-fill",
    title: "Rain Chance",
    text: "30%",
  },
  {
    icon: "meteocons:pressure-high-alt-fill",
    title: "Pressure",
    text: "720hpa",
  },
  { icon: "meteocons:uv-index", title: "UV Index", text: "2.00" },
];
</script>
<template>
  <div>
    <!--Search popup overlay-->
    <Transition name="slide-fade" :duration="300">
      <div
        id="overlay"
        v-if="isSearchDialogOpen"
        @click="
          (event) => {
            if (event.id == 'undefined') isSearchDialogOpen = false;
          }
        "
        class="outer fixed z-50 left-0 top-0 right-0 bottom-0 flex justify-center bg-black bg-opacity-50"
      >
        <!-- search popup card-->
        <div
          class="inner w-full mx-8 mt-24 md:w-[450px] md:mt-60 flex flex-col h-fit bg-white rounded-md overflow-clip"
        >
          <!-- search searchbox-->
          <div
            id="searchbox"
            class="w-full z-10 bg-white flex items-center space-x-2 px-2 py-2 border-b"
          >
            <Icon name="ic:outline-search" size="30" class="mx-2" />
            <input
              v-model="query"
              type="text"
              class="w-full border-none outline-none"
              placeholder="Search city"
              @input="fetchCities"
            />
            <ProgressBar size="32" v-if="isSearching" />
            <button class="size-10 flex items-center justify-center">
              <Icon name="ic:outline-clear" size="22" />
            </button>
          </div>
          <!-- search results-->
          <TransitionGroup tag="ul" name="fade" class="list-container">
            <ul v-if="cities" id="search-results-cicities">
              <SearchResultItem
                v-for="city in cities"
                :city="city"
                :key="city.id"
                @onEndIconClicked="fetchWeather(item.name)"
              />
            </ul>
          </TransitionGroup>
          <!-- No results div-->
          <div
            v-if="!isSearching && cities.length === 0 && query.trim()"
            class="min-h-60 flex flex-col gap-4 items-center justify-center"
          >
            <Icon name="ic:outline-history" size="24" />
            <h5>No search results</h5>
          </div>
        </div>
      </div>
    </Transition>
    <!-- Loading State -->
    <div
      v-if="loading"
      class="fixed z-[100] bg-white left-0 top-0 right-0 bottom-0 overflow-clip flex p-8 flex-col items-center justify-center space-y-2"
    >
      <ProgressBar size="70" />
    </div>

    <!--Weather app-->
    <Container
      v-else-if="weatherData"
      class="space-y-8 md:space-y-0 scroll-smooth md:flex-row overflow-clip *:p-4"
    >
      <!--Main content-->
      <div class="md:w-full space-y-4">
        <!--Header bar-->
        <div
          class="fixed left-0 top-0 right-0 z-40 md:relative flex items-center rounded px-4 py-2 space-x-2 bg-white text-gray-700"
        >
          <a href="" class="me-auto font-bold text-xl">Shuacane</a>
          <button
            @click="isSearchDialogOpen = true"
            class="size-10 aspect-square flex items-center justify-center rounded-full bg-blue-600 text-white"
          >
            <Icon name="ic:outline-search" size="22" />
          </button>

          <button
            class="size-10 aspect-square flex items-center justify-center rounded-full bg-blue-600 text-white"
          >
            <Icon name="ic:outline-gps-fixed" size="22" />
          </button>
        </div>
        <!--Spacer for mobile-->
        <div class="h-14 md:hidden" />
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
              class="flex items-center justify-between *:flex *:items-center *:space-x-2 *:space-y-2 *:flex-col text-sm md:text-base text-nowrap"
            >
              <!-- Pressure -->
              <span>
                <Icon name="solar:water-line-duotone" size="24" />
                <span> {{ weatherData.current.pressure_mb }} hPa </span>
              </span>

              <!-- Humidity -->
              <span>
                <Icon name="meteocons:raindrops-fill" size="24" />
                <span> {{ weatherData.current.humidity }}% </span>
              </span>

              <!-- Wind Speed -->
              <span>
                <Icon name="meteocons:windsock" size="24" />
                <span> {{ weatherData.current.wind_kph }} km/h </span>
              </span>
            </div>
          </div>
          <!--Map Div-->
          <div
            class="relative md:w-full px-4 py-2 m-2 space-y-2 bg-white bg-opacity-40 backdrop-blur rounded overflow-clip"
          >
            <!--Map content Div-->
            <div id="map" class="aspect-square md:aspect-auto"></div>
          </div>
        </CloudCard>
        <!--Specifics wind speed & others -->
        <ul class="hidden md:grid w-full mt-8 gap-2 md:gap-8 grid-cols-4">
          <li
            v-for="item in specifics"
            class="w-full flex flex-col items-center justify-center text-center rounded-xl aspect-square md:h-[150px] px-4 py-2 bg-gray-200"
          >
            <!--Icon -->
            <Icon :name="item.icon" class="m-4 size-6 md:size-8" />
            <!--Info -->
            <h4 class="text-base md:text-xl">{{ item.text }}</h4>
            <span class="text-sm md:text-base opacity-50">{{
              item.title
            }}</span>
          </li>
        </ul>

        <!--Desktop footer-->
        <Footer class="hidden md:flex mt-auto align-bottom" />
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
              class="flex flex-col items-center justify-center space-y-2 px-4 py-2 rounded duration-300 transition-colors hover:bg-[#ffd89e] hover:bg-opacity-30 hover:cursor-pointer"
              :class="{ 'bg-[#ffd89e] ': index == 0 }"
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
          <!--Daily focast list-->
          <ul class="w-full flex flex-col">
            <li
              v-for="(day, index) in forecastData.forecast.forecastday"
              :key="index"
              class="flex items-center px-4 py-2"
            >
              <div class="w-full flex flex-col">
                <span class="text-base font-bold">{{
                  new Date(day.date).toLocaleDateString("en-US", {
                    weekday: "long",
                  })
                }}</span>
                <span class="text-sm opacity-60"
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
        <!--Mobile footer-->
        <Footer class="md:hidden mt-auto align-bottom" />
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
        @click="fetchWeather(query)"
        class="w-fit flex items-center justify-center px-4 py-2 rounded-full aspect-video transition-all duration-300 bg-blue-500 text-white cursor-pointer hover:bg-opacity-50"
      >
        Retry
      </button>
    </div>
  </div>
</template>
<style>
a {
  @apply w-fit h-fit transition-all duration-300 text-blue-800 hover:text-blue-300;
}
.scroll-smooth::-webkit-scrollbar {
  @apply h-0;
}
input::placeholder {
  color: #333333;
}
.grid-responsive {
  grid-area: span;
  grid-template-columns: repeat(auto-fill, minmax(80px, 1fr));
}
.slide-fade-enter-active .inner {
  transition-delay: 0.1s;
  transition: all 0.3s;
  transform: translateZ(0);
}

.slide-fade-leave-active .inner {
  transition: all 0.3s cubic-bezier(1, 0.5, 0.8, 1);
  transform: translateZ(0);
}
.slide-fade-enter-from .inner,
.slide-fade-leave-to .inner {
  transform: translateY(100%) translateZ(0);
  opacity: 0;
}
/** list animations */
.list-container {
  position: relative;
  padding: 0;
  list-style-type: none;
}

/* 1. declare transition */
.fade-move,
.fade-enter-active,
.fade-leave-active {
  transition: all 0.5s cubic-bezier(0.55, 0, 0.1, 1);
}

/* 2. declare enter from and leave to state */
.fade-enter-from,
.fade-leave-to {
  opacity: 0;
  transform: scaleY(0.01) translate(30px, 0);
}

/* 3. ensure leaving items are taken out of layout flow so that moving
      animations can be calculated correctly. */
.fade-leave-active {
  position: absolute;
}
</style>
