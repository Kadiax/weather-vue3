<template>
  <div class="temp-card">
    <div class="data">
      <div class="weather">
        <div class="location">
          <div class="city-name">{{ city }}</div>
          <div class="date-time">
            {{
              new Date(weatherData.currentTime).toLocaleDateString("en-us", {
                weekday: "short",
                day: "2-digit",
                month: "long",
                year: "numeric",
              })
            }}<br />
            {{
              new Date(weatherData.currentTime).toLocaleTimeString("en-us", {
                timeStyle: "short",
              })
            }}
          </div>
        </div>
        <div class="temperature">
          <div class="temp-details">
            <div class="temp-value">
              {{ Math.round(weatherData.main.temp) }}
              <span class="temp-deg">&deg;</span>
              <span class="temp-unit"> C</span>
            </div>
            <div class="cloud-icon">
              <img
                src="../../assets/images/cloud_svgrepo-big.com.svg"
                alt="cloud"
              />
            </div>
          </div>
          <div class="high-low">
            High: {{ Math.round(weatherData.main.temp_max) }}&deg; Low:
            {{ Math.round(weatherData.main.temp_min) }}&deg;
          </div>
        </div>
      </div>
      <div class="icon">
        <img
          class="weather-icon"
          :src="`http://openweathermap.org/img/wn/${weatherData.weather[0].icon}@2x.png`"
          alt="weather-icon"
        />
        <div class="description">
          <div class="description-title">
            {{ toCapitalize(weatherData.weather[0].description) }}
          </div>
          <div class="feels-like">
            Feels Like {{ Math.round(weatherData.main.feels_like) }}
          </div>
        </div>
      </div>
    </div>
    <img
      class="small-cloud-icon"
      src="../../assets/images/cloud_svgrepo.com.svg"
      alt="cloud"
    />
  </div>
</template>
<script setup>
import { toCapitalize } from "@/utilities/toCapitalize";

const props = defineProps({
  city: {
    type: String,
    default: "",
  },
  weatherData: {
    type: Object,
    default: () => ({}),
  },
});
</script>
