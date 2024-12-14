<template>
  <div class="page-content overview">
    <div class="overview-header">
      <h2 class="main-title">Overview</h2>
      <div class="buttons">
        <RouterLink :to="{ name: 'home' }">
          <div class="button go-back">
            <span><i class="fa-solid fa-circle-arrow-left"></i></span>
            <span class="title">Go back</span>
          </div>
        </RouterLink>
        <div v-if="route.query.preview" class="button save" @click="addCity()">
          <span><i class="fa-regular fa-floppy-disk"></i></span>
          <span class="title">Save</span>
        </div>
      </div>
    </div>
    <Suspense>
      <AsyncCityView />
      <template #fallback>
        <CityViewSkeleton />
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import AsyncCityView from "@/components/cityView/AsyncCityView.vue";
import CityViewSkeleton from "@/components/cityView/skeleton/CityViewSkeleton.vue";
import { RouterLink, useRoute, useRouter } from "vue-router";
import { ref } from "vue";
import { uid } from "uid";

const route = useRoute();
const router = useRouter();

const savedCities = ref([]);

const addCity = () => {
  if (localStorage.getItem("savedCities")) {
    savedCities.value = JSON.parse(localStorage.getItem("savedCities"));
  }
  const locationObj = {
    id: uid(),
    state: route.params.state,
    city: route.params.city,
    coords: {
      lat: route.query.lat,
      lng: route.query.lng,
    },
  };
  savedCities.value.push(locationObj);
  localStorage.setItem("savedCities", JSON.stringify(savedCities.value));
  let query = Object.assign({}, route.query);
  delete query.preview;
  query.id = locationObj.id;
  router.replace({ query });
};
</script>
