<template>
  <div class="pollution-card">
    <div class="title">Pollution</div>
    <div
      class="polluant"
      v-for="(value, name) in polluantData.list[0].components"
      :key="name"
    >
      <div class="tooltip-container">
        <p class="name tooltip-trigger">
          {{ toCapitalize(String(name).replace("_", ".")) }}
          <span class="question tooltip-icon">?</span>
        </p>

        <!-- Tooltip -->
        <div class="tooltip-content">
          <h2>{{ polluantDetails[name].fullName }}</h2>
        </div>
      </div>

      <progress
        class="progress-bar"
        :value="(value / polluantDetails[name].thresholds.poor) * 100"
        max="100"
        :style="getPollutantColor(name, value)"
      ></progress>
      <div class="values">
        <div class="value">{{ value }}</div>
        <div class="separator">/</div>
        <div class="value-max">
          {{ polluantDetails[name].thresholds.poor }}
        </div>
        <div class="unit">μg/m3</div>
      </div>
    </div>
  </div>
</template>

<script setup lang="ts">
import { toCapitalize } from "@/utilities/toCapitalize";

const props = defineProps({
  polluantData: {
    type: Object,
    default: () => ({}),
  },
});

const polluantDetails = {
  co: {
    fullName: "Carbon monoxide",
    thresholds: { good: 4400, fair: 9400, moderate: 12400, poor: 15400 },
  },
  no: {
    fullName: "Nitrogen monoxide",
    thresholds: { good: 50, fair: 70, moderate: 80, poor: 100 },
  },
  no2: {
    fullName: "Nitrogen dioxide",
    thresholds: { good: 40, fair: 70, moderate: 150, poor: 200 },
  },
  o3: {
    fullName: "Ozone",
    thresholds: { good: 60, fair: 100, moderate: 140, poor: 180 },
  },
  so2: {
    fullName: "Sulphur dioxide",
    thresholds: { good: 20, fair: 80, moderate: 250, poor: 350 },
  },
  pm2_5: {
    fullName: "Fine particles matter",
    thresholds: { good: 10, fair: 25, moderate: 50, poor: 75 },
  },
  pm10: {
    fullName: "Coarse particulate matter",
    thresholds: { good: 20, fair: 50, moderate: 100, poor: 200 },
  },
  nh3: {
    fullName: "Ammonia",
    thresholds: { good: 50, fair: 150, moderate: 100, poor: 200 },
  },
};

const getPollutantColor = (name, value) => {
  const thresholds = polluantDetails[name].thresholds;

  if (value <= thresholds.fair) {
    return { "--progress-color": "var(--color-text-success)" }; // good
  } else if (value >= thresholds.fair && value <= thresholds.moderate) {
    return { "--progress-color": "var(--color-text-warning-secondary)" }; // moderate
  } else {
    return { "--progress-color": "var(--color-text-error)" }; // poor
  }
};
</script>
