<template>
  <div class="sun-card">
    <div class="day-info-container">
      <div class="day-info">
        <div class="label">Sunrise</div>
        <div class="time-info">
          <div class="time">
            {{
              new Date(weatherData.sys.sunrise)
                .toLocaleTimeString("en-us", {
                  timeStyle: "short",
                })
                .split(" ")[0]
            }}
          </div>
          <div class="period">
            {{
              new Date(weatherData.sys.sunrise)
                .toLocaleTimeString("en-us", {
                  timeStyle: "short",
                })
                .split(" ")[1]
            }}
          </div>
        </div>
      </div>
      <div class="day-info">
        <div class="label">Sunset</div>
        <div class="time-info">
          <div class="time">
            {{
              new Date(weatherData.sys.sunset)
                .toLocaleTimeString("en-us", {
                  timeStyle: "short",
                })
                .split(" ")[0]
            }}
          </div>
          <div class="period">
            {{
              new Date(weatherData.sys.sunset)
                .toLocaleTimeString("en-us", {
                  timeStyle: "short",
                })
                .split(" ")[1]
            }}
          </div>
        </div>
      </div>
      <div class="day-info">
        <div class="label">Length of day</div>
        <div class="time-info">
          <div class="length">{{ weatherData.sys.dayLength }}</div>
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
