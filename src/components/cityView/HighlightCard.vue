<template>
  <div class="highlight-card">
    <div class="title">Today Highlight</div>
    <div class="cards">
      <div class="card">
        <div class="card-header">
          <div class="header-title">Pressure</div>
          <div class="header-icon">
            <img
              src="../../assets/images/pressure-icon.svg"
              alt="pressure-icon"
            />
          </div>
        </div>
        <div class="card-value">
          <span class="value-number">{{ weatherData.main.pressure }}</span>
          <span class="value-unit">hpa</span>
        </div>
      </div>
      <div class="card">
        <div class="card-header">
          <div class="header-title">Air Quality Index</div>
          <div class="header-icon">
            <img
              class="air-img"
              src="../../assets/images/aqi.svg"
              alt="aqi-icon"
            />
          </div>
        </div>
        <div class="aqi card-value">
          <span class="value-number">{{ aqi }}</span>
        </div>
      </div>
      <div class="card">
        <div class="card-header">
          <div class="header-title">Wind Speed</div>
          <div class="header-icon">
            <img src="../../assets/images/wind-icon.svg" alt="wind-icon" />
          </div>
        </div>
        <div class="card-value">
          <span class="value-number">{{ weatherData.wind.speed }}</span>
          <span class="value-unit">m/s</span>
        </div>
      </div>
      <div class="card">
        <div class="card-header">
          <div class="header-title">Humidity</div>
          <div class="header-icon">
            <img
              src="../../assets/images/humidity-icon.svg"
              alt="humidity-icon"
            />
          </div>
        </div>
        <div class="humidity card-value">
          <span class="value-number">{{ weatherData.main.humidity }}</span>
          <span class="value-unit">%</span>
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
  aqi: Number,
});
</script>
