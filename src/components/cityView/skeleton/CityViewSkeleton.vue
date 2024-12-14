<template>
  <div class="overview-content">
    <div class="figures">
      <div class="main">
        <TempCardSkeleton />
        <HourlyCardSkeleton />
        <SunCardSkeleton />
      </div>
      <div class="side">
        <HighlightCardSkeleton />
        <PollutionCardSkeleton />
      </div>
    </div>
  </div>
</template>

<script setup>
import TempCardSkeleton from "../skeleton/TempCardSkeleton.vue";
import HourlyCardSkeleton from "../skeleton/HourlyCardSkeleton.vue";
import SunCardSkeleton from "../skeleton/SunCardSkeleton.vue";
import HighlightCardSkeleton from "../skeleton/HighlightCardSkeleton.vue";
import PollutionCardSkeleton from "../skeleton/PollutionCardSkeleton.vue";
</script>
