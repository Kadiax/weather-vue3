<template>
  <div class="flex flex-col flex-1 items-center">
    <!--Banner-->
    <div
      v-if="route.query.preview"
      class="text-white p-4 bg-weather-secondary w-full text-center"
    >
      <p>
        You are currently previewing this city, click the "+" icon to start
        tracking this city.
      </p>
    </div>
    <!--Weather Overview-->
    <div class="flex flex-col items-center text-white py-12">
      <h1 class="text-4xl mb-2">{{ route.params.city }}</h1>
      <p class="text-sm mb-12">
        {{
          new Date(weatherData.currentTime).toLocaleDateString("en-us", {
            weekday: "short",
            day: "2-digit",
            month: "long",
          })
        }}
        {{
          new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
            timeStyle: "short",
          })
        }}
      </p>
      <p class="text-8xl mb-8">{{ Math.round(weatherData.main.temp) }}&deg;</p>
      <p>Feels like {{ Math.round(weatherData.main.feels_like) }}&deg;</p>
      <p class="capitalize">{{ weatherData.weather[0].description }}</p>
      <img
        class="w-[150px] h-auto"
        :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
        alt=""
      />
    </div>
    <hr class="border-white border-opacity-10 border w-full" />
    <!--Hourly Weather-->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Hourly Weather (5 days)</h2>
        <div class="flex gap-10 overflow-x-scroll">
          <div
            v-for="hourData in weatherData.hours"
            :key="hourData.dt"
            class="flex flex-col gap-4 items-center"
          >
            <p class="whitespace-nowrap text-md text-center">
              {{
                new Date(hourData.currentTime).toLocaleDateString("en-us", {
                  weekday: "short",
                  day: "2-digit",
                  month: "short",
                })
              }}<br />
              {{
                new Date(hourData.currentTime).toLocaleTimeString("en-us", {
                  hour: "numeric",
                })
              }}
            </p>
            <img
              :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
              alt=""
              class="w-10 h-10 object-scale-down"
            />
            <p class="text-xl mb-5">
              {{ Math.round(hourData.main.temp) }}&deg;
            </p>
          </div>
        </div>
      </div>
    </div>
    <hr class="border-white border-opacity-10 border w-full" />
    <!-- Pollution -->
    <div class="max-w-screen-md w-full py-12">
      <div class="mx-8 text-white">
        <h2 class="mb-4">Pollution</h2>
        <div class="flex flex-row justify-between">
          <div
            v-for="(value, name) in polluantData.list[0].components"
            :key="name"
            class="flex flex-col items-center"
          >
            <div
              tabindex="0"
              class="relative group inline-block cursor-default"
            >
              <p class="capitalize">
                {{ name.replace("_", ".") }}
                <span
                  class="absolute text-xs text-blue-950 top-0 right-0 transform translate-x-1/2 -translate-y-1/2"
                  >?</span
                >
              </p>

              <!-- Tooltip -->
              <div
                class="absolute bottom-full left-1/2 transform -translate-x-1/2 mb-2 hidden group-hover:block bg-gray-800 text-white text-xs rounded py-1 px-2 transition-opacity duration-300 opacity-0 group-hover:opacity-100 w-[110px]"
              >
                <h2>{{ polluantDetails[name].fullName }} :</h2>
                <span class="text-xs text-green-500">
                  Good: &lt;{{ polluantDetails[name].thresholds.good }}
                </span>
                <br />
                <span class="text-xs text-yellow-500">
                  Fair: &lt;{{ polluantDetails[name].thresholds.fair }} </span
                ><br />
                <span class="text-xs text-orange-500">
                  Moderate: &lt;{{ polluantDetails[name].thresholds.moderate }}
                </span>
                <span class="text-xs text-red-500">
                  Bad: &gt;{{ polluantDetails[name].thresholds.moderate }}
                </span>
              </div>
            </div>
            <i
              :class="[
                'fa-solid fa-circle text-2xl my-5',
                getPollutantColor(name, value),
              ]"
            ></i>
            <p>
              {{ value }}
            </p>
            <p>μg/m3</p>
          </div>
        </div>
      </div>
    </div>

    <div
      v-if="!route.query.preview"
      class="flex items-center gap-2 py-12 text-white cursor-pointer duration-150 hover:text-red-500"
      @click="removeCity"
    >
      <i class="fa-solid fa-trash"></i>
      <p>Remove City</p>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";

const route = useRoute();
const router = useRouter();
const openweathermapAPIKey = import.meta.env.VITE_OPEN_WEATHER_MAP_API_KEY;
const polluantDetails = {
  co: {
    fullName: "Carbon monoxide",
    thresholds: { good: 4400, fair: 9400, moderate: 12400 },
  },
  no: {
    fullName: "Nitrogen monoxide",
    thresholds: { good: 1, fair: 1.5, moderate: 100 },
  },
  no2: {
    fullName: "Nitrogen dioxide",
    thresholds: { good: 40, fair: 70, moderate: 150 },
  },
  o3: { fullName: "Ozone", thresholds: { good: 60, fair: 100, moderate: 140 } },
  so2: {
    fullName: "Sulphur dioxide",
    thresholds: { good: 20, fair: 80, moderate: 250 },
  },
  pm2_5: {
    fullName: "Fine particles matter",
    thresholds: { good: 10, fair: 25, moderate: 50 },
  },
  pm10: {
    fullName: "Coarse particulate matter",
    thresholds: { good: 20, fair: 50, moderate: 100 },
  },
  nh3: {
    fullName: "Ammonia",
    thresholds: { good: 1, fair: 1.5, moderate: 200 },
  },
};
const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&appid=${openweathermapAPIKey}&units=metric`
    );
    const weatherDataHourly = await axios.get(
      `https://api.openweathermap.org/data/2.5/forecast?lat=${route.query.lat}&lon=${route.query.lng}&appid=${openweathermapAPIKey}&units=metric`
    );

    // cal current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.dt * 1000 + localOffset;
    weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone;

    // cal hourly weather offset
    weatherDataHourly.data.list.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone;
    });
    weatherData.data.hours = Array.from(weatherDataHourly.data.list);

    // Flicker delay
    await new Promise((res) => setTimeout(res, 500));

    return weatherData.data;
  } catch (error) {
    console.log(error);
  }
};
const getPolluants = async () => {
  try {
    const polluantData = await axios.get(
      `https://api.openweathermap.org/data/2.5/air_pollution?lat=${route.query.lat}&lon=${route.query.lng}&appid=${openweathermapAPIKey}`
    );
    return polluantData.data;
  } catch (error) {
    console.log(error);
  }
};
const getPollutantColor = (name, value) => {
  const thresholds = polluantDetails[name].thresholds;

  if (value <= thresholds.good) {
    return "text-green-500"; // good
  } else if (value <= thresholds.fair) {
    return "text-yellow-500"; // fair
  } else if (value <= thresholds.moderate) {
    return "text-orange-500"; // moderate
  } else {
    return "text-red-500"; // poor
  }
};
const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({ name: "home" });
};

const weatherData = await getWeatherData();
const polluantData = await getPolluants();
</script>
