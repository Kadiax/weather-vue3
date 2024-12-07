<template>
  <div class="overview-content">
    <div class="figures">
      <div class="main">
        <div class="temp-card">
          <div class="data">
            <div class="weather">
              <AnimatedPlaceholder width="12rem" height="12rem" />
            </div>
            <div class="icon">
              <AnimatedPlaceholder width="6rem" height="6rem" />
              <div class="description">
                <div class="description-title">
                  <AnimatedPlaceholder />
                </div>
                <div class="feels-like">
                  <AnimatedPlaceholder />
                </div>
              </div>
            </div>
          </div>
        </div>
        <div class="hourly-card">
          <div class="title">
            <AnimatedPlaceholder width="8rem" />
          </div>
          <div class="weather-cards">
            <div class="weather-card">
              <AnimatedPlaceholder width="40rem" height="7rem" />
            </div>
          </div>
        </div>
        <div class="sun-card">
          <div class="day-info-container">
            <div class="day-info">
              <div class="label">
                <AnimatedPlaceholder />
              </div>
              <div class="time-info">
                <AnimatedPlaceholder width="6rem" height="6rem" />
              </div>
            </div>
            <div class="day-info">
              <div class="label"><AnimatedPlaceholder /></div>
              <div class="time-info">
                <AnimatedPlaceholder width="6rem" height="6rem" />
              </div>
            </div>
            <div class="day-info">
              <div class="label">
                <AnimatedPlaceholder />
              </div>
              <div class="time-info">
                <AnimatedPlaceholder width="6rem" height="6rem" />
              </div>
            </div>
          </div>
        </div>
      </div>
      <div class="side">
        <div class="highlight-card">
          <div class="title">
            <AnimatedPlaceholder width="7rem" />
          </div>
          <div class="cards">
            <div class="card">
              <div class="card-header">
                <div class="header-title">
                  <AnimatedPlaceholder />
                </div>
              </div>
              <div class="card-value">
                <AnimatedPlaceholder width="6rem" height="3rem" />
              </div>
            </div>
            <div class="card">
              <div class="card-header">
                <div class="header-title">
                  <AnimatedPlaceholder />
                </div>
              </div>
              <div class="aqi card-value">
                <AnimatedPlaceholder width="6rem" height="3rem" />
              </div>
            </div>
            <div class="card">
              <div class="card-header">
                <div class="header-title">
                  <AnimatedPlaceholder />
                </div>
              </div>
              <div class="card-value">
                <AnimatedPlaceholder width="6rem" height="3rem" />
              </div>
            </div>
            <div class="card">
              <div class="card-header">
                <div class="header-title">
                  <AnimatedPlaceholder />
                </div>
              </div>
              <div class="humidity card-value">
                <AnimatedPlaceholder width="6rem" height="3rem" />
              </div>
            </div>
          </div>
        </div>
        <div class="pollution-card">
          <div class="title">
            <AnimatedPlaceholder width="7rem" />
          </div>
          <AnimatedPlaceholder class="skeleton" width="auto" height="15rem" />
        </div>
      </div>
    </div>
  </div>
</template>

<script setup>
import AnimatedPlaceholder from "./AnimatedPlaceholder.vue";
</script>
