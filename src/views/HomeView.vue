<template>
  <div class="page-content home">
    <h2 class="main-title">Home</h2>

    <div class="search">
      <div class="search-bar">
        <i class="fas fa-search"></i>
        <input
          v-model="searchQuery"
          @input="getSearchResults"
          type="text"
          placeholder="Search for a city or state"
        />
      </div>
      <ul class="search-results">
        <p v-if="searchError">Sorry, went wrong please try again.</p>
        <p v-if="!searchError && mapBoxSearchResults?.length === 0">
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            v-for="searchResult in mapBoxSearchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.properties.full_address }}
          </li>
        </template>
      </ul>
    </div>

    <Suspense>
      <CityList />
      <template #fallback>
        <CityCardListSkeleton />
      </template>
    </Suspense>
  </div>
</template>

<script setup>
import axios from "axios";
import { ref } from "vue";
import { useRouter } from "vue-router";
import CityList from "@/components/CityList.vue";
import CityCardListSkeleton from "@/components/CityCardListSkeleton.vue";

const router = useRouter();
const mapBoxAPIKey = import.meta.env.VITE_MAP_BOX_API_KEY;
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapBoxSearchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/search/geocode/v6/forward?q=${searchQuery.value}&access_token=${mapBoxAPIKey}&types=place`
        );
        mapBoxSearchResults.value = result.data.features;
      } catch (error) {
        searchError.value = true;
      }
      return;
    }
    mapBoxSearchResults.value = null;
  }, 300);
};

const previewCity = (searchResult) => {
  const [city, state] = searchResult.properties.full_address.split(",");
  router.push({
    name: "cityView",
    params: { state: state.replaceAll(" ", ""), city: city },
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    },
  });
};
</script>
<style>
/*Home page*/
.home {
  display: flex;
  flex-direction: column;
  justify-content: center; /* Center the container vertically */
  align-items: center; /* Center the container horizontally */
}

.home .search-bar {
  background: var(--color-bg-secondary);
  width: 518px;
  height: 51px;
  display: inline-flex;
  border-radius: 50px;
  justify-content: flex-start;
  align-items: center;
  gap: 24px;
}

.home .search-bar i {
  font-size: 18px;
  margin-left: 20px;
}

.search-bar input {
  border: none;
  background: transparent;
  outline: none;
  margin-left: 10px;
  color: var(--color-text-secondary);
  width: 100%;
  font-size: 14px;
}

.search-results {
  width: 518px;
  position: fixed;
  background-color: var(--color-bg-secondary);
  padding-left: 15px;
}

.search {
  margin-top: 60px;
  border-radius: 50px;
  background-color: var(--color-bg-secondary);
}

/*Mobiles*/
@media (min-width: 360px) and (max-width: 700px) {
  .home .search {
    width: 100%;
    margin-top: 25px;
  }

  .home .search-bar,
  .home .search-results {
    width: 93%;
  }
}
</style>
