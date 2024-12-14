<template>
  <div class="hourly-card">
    <div class="title">Hourly Weather (5 days)</div>
    <div class="weather-cards">
      <div
        class="weather-card"
        v-for="hourData in weatherData.hours"
        :key="hourData.dt"
      >
        <div class="weather-details">
          <div class="weather-time">
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
          </div>
          <img
            class="weather-icon"
            :src="`http://openweathermap.org/img/wn/${hourData.weather[0].icon}@2x.png`"
            alt="Weather Icon"
          />
          <div class="weather-temp">
            {{ Math.round(hourData.main.temp) }}&deg;
          </div>
        </div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
const props = defineProps({
  weatherData: {
    type: Object,
    default: () => ({}),
  },
});
</script>
