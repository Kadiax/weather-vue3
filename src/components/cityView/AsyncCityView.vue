<template>
  <div class="overview-content">
    <div class="figures">
      <div class="main">
        <TempCard :city="route.params.city" :weatherData="weatherData" />
        <HourlyCard :weatherData="weatherData" />
        <SunCard :weatherData="weatherData" />
      </div>
      <div class="side">
        <HighlightCard
          :weatherData="weatherData"
          :aqi="polluantData.list[0].main.aqi"
        />
        <PollutionCard :polluantData="polluantData" />
      </div>
    </div>
    <div v-if="!route.query.preview" class="remove-button">
      <div class="button remove" @click="removeCity">
        <span><i class="fa-solid fa-trash-can"></i></span>
        <span class="title">Remove</span>
      </div>
    </div>
  </div>
</template>

<script setup>
import axios from "axios";
import { useRoute, useRouter } from "vue-router";
import { computed } from "vue";
import TempCard from "../cityView/TempCard.vue";
import HourlyCard from "../cityView/HourlyCard.vue";
import SunCard from "../cityView/SunCard.vue";
import HighlightCard from "../cityView/HighlightCard.vue";
import PollutionCard from "../cityView/PollutionCard.vue";

const route = useRoute();
const router = useRouter();
const openweathermapAPIKey = import.meta.env.VITE_OPEN_WEATHER_MAP_API_KEY;

const getWeatherData = async () => {
  try {
    const weatherData = await axios.get(
      `https://api.openweathermap.org/data/2.5/weather?lat=${route.query.lat}&lon=${route.query.lng}&appid=${openweathermapAPIKey}&units=metric`
    );
    const weatherDataHourly = await axios.get(
      `https://api.openweathermap.org/data/2.5/forecast?lat=${route.query.lat}&lon=${route.query.lng}&appid=${openweathermapAPIKey}&units=metric`
    );

    // calculate the current date & time
    const localOffset = new Date().getTimezoneOffset() * 60000;
    const utc = weatherData.data.dt * 1000 + localOffset;
    weatherData.data.currentTime = utc + 1000 * weatherData.data.timezone;

    // calculate the hourly weather offset
    weatherDataHourly.data.list.forEach((hour) => {
      const utc = hour.dt * 1000 + localOffset;
      hour.currentTime = utc + 1000 * weatherData.data.timezone;
    });
    weatherData.data.hours = Array.from(weatherDataHourly.data.list);

    // calculate the current sunrise date & time
    const utcSunrise = weatherData.data.sys.sunrise * 1000 + localOffset;
    weatherData.data.sys.sunrise =
      utcSunrise + 1000 * weatherData.data.timezone;

    // calculate the current sunset date & time
    const utcSunset = weatherData.data.sys.sunset * 1000 + localOffset;
    weatherData.data.sys.sunset = utcSunset + 1000 * weatherData.data.timezone;

    // calculate the day length
    weatherData.data.sys.dayLength = computed(() => {
      const sunrise = new Date(weatherData.data.sys.sunrise);
      const sunset = new Date(weatherData.data.sys.sunset);

      // Get the difference in milliseconds
      const diff = sunset - sunrise;

      // Convert to hours and minutes
      const hours = Math.floor(diff / (1000 * 60 * 60));
      const minutes = Math.floor((diff % (1000 * 60 * 60)) / (1000 * 60));

      return `${hours}h ${minutes}m`;
    });

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

const removeCity = () => {
  const cities = JSON.parse(localStorage.getItem("savedCities"));
  const updatedCities = cities.filter((city) => city.id !== route.query.id);
  localStorage.setItem("savedCities", JSON.stringify(updatedCities));
  router.push({ name: "home" });
};

const weatherData = await getWeatherData();
const polluantData = await getPolluants();
</script>

<style>
/* Overview Page */
.overview-header {
  align-self: flex-start;
  display: flex;
  justify-content: space-between;
}

.overview-header .buttons {
  display: flex;
  align-items: center;
  gap: 34px;
  margin-top: 13px;
}

.overview-header .buttons .save i {
  color: var(--color-text-success);
}

.overview-content {
  margin-top: 20px;
}

.overview-content .figures {
  width: auto;
  height: auto;
  display: grid;
  grid-template-columns: 1.5fr 1.2fr;
  grid-gap: 25px 25px;
  padding: 20px;
}

.overview-content .figures .main {
  min-width: 28%;
  height: 100%;
}

.overview-content .figures .main .temp-card {
  background: var(--color-bg-secondary);
  border-radius: 40px;
  height: fit-content;
  padding: 15px 27px;
}

.overview-content .figures .main .temp-card .small-cloud-icon {
  width: 32px;
  height: 32px;
  margin-left: 367px;
}
.overview-content .figures .main .temp-card .data {
  display: flex;
  flex-direction: row;
  justify-content: space-between;
}

.overview-content .figures .main .temp-card .weather .location .city-name {
  color: var(--color-text-primary);
  font-size: 27px;
  font-family: Inter, sans-serif;
  font-weight: 500;
  word-wrap: break-word;
}

.overview-content .figures .main .temp-card .weather .location .date-time {
  color: var(--color-text-primary);
  font-size: 16px;
  font-family: Inter, sans-serif;
  font-weight: 400;
  word-wrap: break-word;
}

.overview-content
  .figures
  .main
  .temp-card
  .weather
  .temperature
  .temp-details {
  display: flex;
  flex-direction: row;
}

.overview-content
  .figures
  .main
  .temp-card
  .weather
  .temperature
  .temp-details
  .temp-value {
  color: var(--color-text-primary);
  font-size: 64px;
  font-family: Inter, sans-serif;
  font-weight: 500;
  word-wrap: break-word;
}

.overview-content
  .figures
  .main
  .temp-card
  .weather
  .temperature
  .temp-details
  .temp-value
  .temp-deg {
  padding-left: 0px;
  margin-left: -9px;
}

.overview-content
  .figures
  .main
  .temp-card
  .weather
  .temperature
  .temp-details
  .temp-value
  .temp-unit {
  padding-left: 0px;
  margin-left: -9px;
}

.overview-content
  .figures
  .main
  .temp-card
  .weather
  .temperature
  .temp-details
  .cloud-icon {
  padding-left: 0px;
  margin-left: 30px;
}

.overview-content .figures .main .temp-card .icon .description {
  display: flex;
  flex-direction: column;
  justify-content: flex-end;
  align-items: flex-end;
  gap: 3px;
  height: 66px;
}

.overview-content .figures .main .temp-card .icon .weather-icon {
  width: 200px;
  height: 200px;
  position: relative;
}

.overview-content
  .figures
  .main
  .temp-card
  .icon
  .description
  .description-title {
  color: var(--color-text-primary);
  font-size: 32px;
  font-family: Inter, sans-serif;
  font-weight: 400;
  word-wrap: break-word;
}

.overview-content .figures .main .temp-card .icon .description .feels-like {
  color: var(--color-text-primary);
  font-size: 16px;
  font-family: Inter, sans-serif;
  font-weight: 400;
  word-wrap: break-word;
}

.overview-content .figures .main .temp-card .weather .temperature .high-low {
  color: var(--color-text-primary);
  font-size: 16px;
  font-family: Inter, sans-serif;
  font-weight: 400;
  word-wrap: break-word;
}

.overview-content .figures .main .hourly-card {
  background: var(--color-bg-secondary);
  display: flex;
  flex-direction: column;
  gap: 20px;
  height: fit-content;
  width: auto;
  padding: 15px 27px;
  border-radius: 40px;
  margin-top: 30px;
}

.overview-content .figures .main .hourly-card .title {
  color: var(--color-text-primary);
  font-size: 20px;
  font-family: Inter, sans-serif;
  font-weight: 400;
}

.overview-content .figures .main .hourly-card .weather-cards {
  height: fit-content;
  padding: 15px 17px;
  display: flex;
  gap: 7px;
  overflow-x: auto;
}

.overview-content .figures .main .hourly-card .weather-cards .weather-card {
  height: 145px;
  padding: 10px 8px;
  background: rgba(255, 255, 255, 0.06);
  box-shadow: 5px 6px 6.8px 1px rgba(0, 0, 0, 0.17);
  border-radius: 15px;
  border: 0.5px solid rgba(233.75, 233.75, 233.75, 0.54);
  display: flex;
  align-items: center;
  justify-content: center;
}

.overview-content
  .figures
  .main
  .hourly-card
  .weather-cards
  .weather-card
  .weather-details {
  width: 30px;
  display: flex;
  flex-direction: column;
  gap: 16px;
}

.overview-content
  .figures
  .main
  .hourly-card
  .weather-cards
  .weather-card
  .weather-details
  .weather-time {
  text-align: center;
  color: var(--color-text-primary);
  font-size: 8px;
  font-family: Inter, sans-serif;
  font-weight: 800;
}

.overview-content
  .figures
  .main
  .hourly-card
  .weather-cards
  .weather-card
  .weather-details
  .weather-icon {
  height: 30px;
  align-self: center;
}

.overview-content
  .figures
  .main
  .hourly-card
  .weather-cards
  .weather-card
  .weather-details
  .weather-temp {
  color: var(--color-text-primary);
  font-size: 14px;
  font-family: Inter, sans-serif;
  font-weight: 400;
  text-align: center;
}

.overview-content .figures .main .sun-card .day-info-container {
  width: auto;
  height: auto;
  padding: 17px 20px;
  margin-top: 40px;
  background: var(--color-bg-primary);
  border-radius: 25px;
  display: flex;
  justify-content: flex-start;
  align-items: center;
  gap: 100px;
}

.overview-content .figures .main .sun-card .day-info-container .day-info {
  width: auto;
  display: flex;
  flex-direction: column;
  justify-content: flex-start;
  align-items: flex-start;
  gap: 18px;
}

.overview-content
  .figures
  .main
  .sun-card
  .day-info-container
  .day-info
  .label {
  color: var(--color-text-secondary);
  font-size: 20px;
  font-family: Inter, sans-serif;
  font-weight: 500;
  text-align: center;
}

.overview-content
  .figures
  .main
  .sun-card
  .day-info-container
  .day-info
  .time-info {
  display: flex;
  align-items: flex-end;
  gap: 15px;
  color: var(--color-text-primary);
  font-size: 32px;
  font-family: Inter, sans-serif;
  font-weight: 500;
}

.overview-content
  .figures
  .main
  .sun-card
  .day-info-container
  .day-info
  .time-info
  .period {
  color: var(--color-text-secondary);
  font-size: 20px;
  font-family: Inter, sans-serif;
  font-weight: 500;
}

.overview-content
  .figures
  .main
  .sun-card
  .day-info-container
  .day-info
  .time-info
  .length {
  font-size: 32px;
}

/*Overview side*/
.overview-content .figures .side {
  min-width: 20%;
  height: 100%;
}

.overview-content .figures .side .highlight-card {
  height: fit-content;
  background: radial-gradient(circle, #162850 0%, #121a2d 100%);
  border-radius: 30px;
  padding: 16px 18px;
}

.overview-content .figures .side .highlight-card .title {
  color: var(--color-text-primary);
  font-size: 20px;
  font-family: Inter, sans-serif;
  font-weight: 400;
}

.overview-content .figures .side .highlight-card .cards {
  margin-top: 15px;
  display: grid;
  grid-template-columns: 1fr 1fr;
  grid-gap: 25px 25px;
}

.overview-content .figures .side .highlight-card .cards .card {
  background: var(--color-bg-secondary);
  height: 170px;
  border-radius: 20px;
  padding: 16px 10px;
  display: flex;
  flex-direction: column;
}

.overview-content .figures .side .highlight-card .cards .card .card-header {
  display: flex;
  justify-content: space-between;
}

.overview-content
  .figures
  .side
  .highlight-card
  .cards
  .card
  .card-header
  .header-title {
  color: var(--color-text-primary);
  font-size: 15px;
  font-family: Inter, sans-serif;
  font-weight: 400;
}
.overview-content .figures .side .highlight-card .cards .card .card-value {
  align-self: center;
  margin-top: 25px;
}

.overview-content .figures .side .highlight-card .cards .card .aqi {
  margin-top: 15px;
}

.overview-content .figures .side .highlight-card .cards .card .humidity {
  margin-top: 32px;
}

.overview-content
  .figures
  .side
  .highlight-card
  .cards
  .card
  .card-value
  .value-number {
  font-size: 37px;
  color: var(--color-text-primary);
  font-family: Inter, sans-serif;
  font-weight: 500;
}

.overview-content
  .figures
  .side
  .highlight-card
  .cards
  .card
  .card-value
  .value-unit {
  font-size: 29px;
  color: var(--color-text-primary);
  margin-left: 10px;
  font-family: Inter, sans-serif;
  font-weight: 500;
}

/*overview pollution */
.overview-content .figures .side .pollution-card {
  background-color: var(--color-bg-primary);
  margin-top: 20px;
  padding: 16px 0px;
}

.overview-content .figures .side .pollution-card .title {
  color: var(--color-text-primary);
  font-size: 20px;
  font-family: Inter;
  font-weight: 400;
  word-wrap: break-word;
  margin-bottom: 15px;
}

.overview-content .figures .side .pollution-card .polluant {
  display: grid;
  grid-template-columns: 0.8fr 3fr 2fr;
  column-gap: 10px;
  margin: 7px 0px;
}

.overview-content .figures .side .pollution-card .polluant .values {
  display: grid;
  grid-template-columns: 0.6fr 0.05fr 0.6fr 1fr;
  column-gap: 7px;
  color: var(--color-text-primary);
  font-size: 12px;
  font-family: Inter, sans-serif;
  font-weight: 700;
}

.overview-content .figures .side .pollution-card .polluant .name {
  color: var(--color-text-primary);
  font-size: 16px;
  font-family: Inter, sans-serif;
  font-weight: 700;
}

.overview-content .figures .side .pollution-card .polluant .name .question {
  color: var(--color-brand-primary);
  font-size: 9px;
  font-family: Inter, sans-serif;
  font-weight: 700;
}

/*Tooltip */
.tooltip-container {
  position: relative;
  display: inline-block;
  cursor: default;
}

.tooltip-icon {
  position: absolute;
  color: var(--color-brand-primary);
  transform: translate(50%, -50%);
}

.tooltip-content {
  position: absolute;
  bottom: 100%;
  left: 50%;
  transform: translateX(-50%);
  background-color: #1f2937;
  font-size: 0.75rem;
  border-radius: 0.375rem;
  padding: 0.25rem 0.5rem;
  width: auto;
  text-align: center;
  display: none;
  opacity: 0;
  transition: opacity 0.3s ease-in-out;
}

.tooltip-container:hover .tooltip-content {
  display: block;
  opacity: 1;
}

/* General styles for the progress bar */
.overview-content .figures .side .pollution-card .polluant .progress-bar {
  align-self: center;
  width: auto;
  height: 8px;
  appearance: none; /* Remove default styling */
  border: none; /* Remove border */
  border-radius: 10px;
  overflow: hidden; /* Ensures rounded corners work correctly */
}

/* Styles for the filled portion (WebKit browsers: Chrome, Safari) */
.overview-content
  .figures
  .side
  .pollution-card
  .polluant
  .progress-bar::-webkit-progress-bar {
  background-color: var(
    --color-text-primary
  ); /* Background of the entire bar */
  border-radius: 10px;
}

.overview-content
  .figures
  .side
  .pollution-card
  .polluant
  .progress-bar::-webkit-progress-value {
  background-color: var(--progress-color); /* Dynamic color */
  border-radius: 10px 0 0 10px; /* Rounds only the left side */
}

/* Styles for the filled portion (Firefox) */
.overview-content
  .figures
  .side
  .pollution-card
  .polluant
  .progress-bar::-moz-progress-bar {
  background-color: var(--progress-color); /* Dynamic color */
  border-radius: 10px 0 0 10px; /* Rounds only the left side */
}

.overview-content .figures .side .pollution-card .polluant .values .value {
  justify-self: end;
}

.overview-content .figures .side .pollution-card .polluant .value-max {
  justify-self: start;
}

.overview-content .figures .side .pollution-card .polluant .values .unit {
  justify-self: end;
}

.overview-content .remove-button {
  display: flex;
  justify-content: center;
  padding: 80px 0px;
}

.overview-content .remove-button i {
  color: var(--color-text-error);
}

/*Tablets*/
@media (max-width: 1024px) {
  .overview-content .overview-header {
    width: 95%;
  }

  .overview-content .figures {
    grid-template-columns: 1fr;
  }

  .overview-content .figures .main .sun-card .day-info-container {
    justify-content: center;
  }

  .overview-content .figures .side .pollution-card {
    justify-self: center;
    width: 100%;
  }

  .overview-content .figures .side .pollution-card .polluant {
    grid-template-columns: 0.8fr 5fr 2fr;
    padding: 0px 5px;
  }

  .overview-content .figures .side .pollution-card .polluant .values {
    grid-template-columns: 1.5fr 0.05fr 1.5fr 1fr;
  }
}

/*Between Tablets Laptops Desktops*/
@media (min-width: 1025px) and (max-width: 1440px) {
  /*Overview page*/
  .overview-content .figures .main .temp-card .icon .weather-icon {
    width: 150px;
    height: 150px;
  }

  .overview-content
    .figures
    .main
    .temp-card
    .weather
    .temperature
    .temp-details
    .temp-value {
    font-size: 60px;
  }

  .overview-content .figures .main .sun-card .day-info-container {
    gap: 5%;
  }

  .overview-content
    .figures
    .side
    .highlight-card
    .cards
    .card
    .card-value
    .value-number {
    font-size: 29px;
  }

  .overview-content
    .figures
    .side
    .highlight-card
    .cards
    .card
    .card-value
    .value-unit {
    font-size: 22px;
  }

  .overview-content
    .figures
    .side
    .highlight-card
    .cards
    .card
    .card-header
    .header-icon
    img {
    width: 30px;
    height: 30px;
  }

  .overview-content
    .figures
    .side
    .highlight-card
    .cards
    .card
    .card-header
    .header-icon
    .air-img {
    width: 40px;
    height: 40px;
  }

  .overview-content
    .figures
    .side
    .highlight-card
    .cards
    .card
    .card-header
    .header-title {
    font-size: 9px;
  }

  .overview-content .figures .side .highlight-card .cards .card {
    height: 100px;
  }

  .overview-content .figures .side .highlight-card .cards .card .card-value {
    margin-top: 0;
  }

  .overview-content .figures .side .pollution-card .polluant {
    grid-template-columns: 0.8fr 2fr 2fr;
  }

  .overview-content .figures .side .pollution-card .polluant .name {
    font-size: 10px;
  }

  .overview-content .figures .side .pollution-card .polluant .values {
    font-size: 8px;
  }

  .overview-content .figures .side .pollution-card .polluant .progress-bar {
    height: 6px;
  }
}

/*Mobiles*/
@media (min-width: 360px) and (max-width: 700px) {
  .overview-content .overview-header,
  .overview-content .figures {
    width: 100%;
  }

  .overview-header .buttons .title {
    display: none;
  }

  .overview-header .buttons {
    margin-right: 25px;
  }

  .overview-header .buttons .button {
    width: 0;
    height: 0;
  }

  .overview-content .figures .main .temp-card {
    padding: 15px 7px;
  }

  .overview-content .figures .main .temp-card .icon .weather-icon {
    width: 150px;
    height: 150px;
  }

  .overview-content
    .figures
    .main
    .temp-card
    .weather
    .temperature
    .temp-details
    .cloud-icon
    img {
    display: none;
  }

  .overview-content .figures .main .temp-card .data {
    background-image: url("images/Group.svg");
    background-position: top right;
    background-repeat: no-repeat;
    background-size: auto;
  }

  .overview-content .figures .main .temp-card .weather .location .date-time {
    font-size: 11px;
  }

  .overview-content
    .figures
    .main
    .temp-card
    .weather
    .temperature
    .temp-details
    .temp-value {
    font-size: 43px;
    margin-top: 20px;
  }

  .overview-content
    .figures
    .main
    .temp-card
    .icon
    .description
    .description-title {
    font-size: 20px;
  }

  .overview-content .figures .main .temp-card .weather .temperature .high-low,
  .overview-content .figures .main .temp-card .icon .description .feels-like {
    font-size: 12px;
  }

  .overview-content .figures .main .temp-card .small-cloud-icon {
    margin-left: 108px;
  }

  .overview-content .figures .main .sun-card .day-info-container {
    gap: 7px;
    padding: 17px 6px;
  }

  .overview-content
    .figures
    .main
    .sun-card
    .day-info-container
    .day-info
    .label {
    font-size: 15px;
  }

  .overview-content
    .figures
    .main
    .sun-card
    .day-info-container
    .day-info
    .time-info,
  .overview-content
    .figures
    .main
    .sun-card
    .day-info-container
    .day-info
    .time-info
    .length {
    font-size: 20px;
  }

  .overview-content
    .figures
    .main
    .sun-card
    .day-info-container
    .day-info
    .time-info
    .period {
    font-size: 12px;
  }

  .overview-content .figures .side .highlight-card {
    padding: 16px 8px;
  }

  .overview-content .figures .side .highlight-card .cards {
    grid-gap: 8px 8px;
  }

  .overview-content .figures .side .highlight-card .cards .card {
    height: auto;
  }

  .overview-content
    .figures
    .side
    .highlight-card
    .cards
    .card
    .card-value
    .value-number {
    font-size: 25px;
  }

  .overview-content
    .figures
    .side
    .highlight-card
    .cards
    .card
    .card-value
    .value-unit {
    font-size: 17px;
    margin-left: 2px;
  }

  .overview-content .figures .side .pollution-card .polluant {
    grid-template-columns: 0.5fr 2fr 2fr;
  }

  .overview-content .figures .side .pollution-card .polluant .values {
    font-size: 10px;
    grid-template-columns: 1.5fr 0.05fr 1.5fr 0.2fr;
  }

  .overview-content .figures .side .pollution-card .polluant .name {
    font-size: 7px;
  }
}
</style>
