<template>
  <main class="container text-white">
      <div
              id="bookmundi_widget_container_"
              class="bookmundi_widget_container"
              data-widget-name="Widget Title"
              data-widget-width="700" 
              data-widget-alignment="horizontal" 
              data-agency-code="" 
              data-agency-url="*" data-widget-type="product" 
              data-product-widget-type="destination" 
              data-tour-type="all" 
              data-tour-limit="3" 
              data-show-search="1" 
              data-selected-search="" 
              data-selected-activity="" 
              data-selected-search-type="destination" 
              data-currency="USD">
            </div>
           

                        <div
              id="bookmundi_widget_container_QdTFsKV24ILZLZIO1zTAQmLCyZfE9Xjc"
              class="bookmundi_widget_container"
              data-widget-name="Nepal Tours"
              data-widget-width="450.00" 
              data-widget-alignment="vertical" 
              data-agency-code="QdTFsKV24ILZLZIO1zTAQmLCyZfE9Xjc" 
              data-agency-url="*" data-widget-type="product" 
              data-product-widget-type="destination" 
              data-tour-type="all" 
              data-tour-limit="4" 
              data-show-search="1" 
              data-selected-search="Nepal" 
              data-selected-activity="" 
              data-selected-search-type="country" 
              data-currency="USD">
            </div>
    <div class="pt-4 mb-8 relative">
      <input
        type="text"
        v-model="searchQuery"
        @input="getSearchResults"
        placeholder="Search for a city or state"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]"
      />
      <ul
        class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults"
      >
        <p class="py-2" v-if="searchError">
          Sorry, something went wrong, please try again.
        </p>
        <p
          class="py-2"
          v-if="!searchError && mapboxSearchResults.length === 0"
        >
          No results match your query, try a different term.
        </p>
        <template v-else>
          <li
            v-for="searchResult in mapboxSearchResults"
            :key="searchResult.id"
            class="py-2 cursor-pointer"
            @click="previewCity(searchResult)"
          >
            {{ searchResult.place_name }}
          </li>
        </template>
      </ul>
    </div>
    <div class="flex flex-col gap-4">
      <Suspense>
        <CityList />
        <template #fallback>
          <CityCardSkeleton />
        </template>
      </Suspense>
    </div>
              

  </main>
</template>
 <script async src="https://assets.bookmundi.com/production/resources/widget/loader.js"></script>
<script async src="https://assets.bookmundi.com/production/resources/widget/loader.js"></script>

<script setup>
import { ref } from "vue";
import axios from "axios";
import { useRouter } from "vue-router";
import CityCardSkeleton from "../components/CityCardSkeleton.vue";
import CityList from "../components/CityList.vue";

const router = useRouter();
const previewCity = (searchResult) => {
  const [city, state] = searchResult.place_name.split(",");
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

const mapboxAPIKey = import.meta.env.VITE_MAPBOX_ID;
const searchQuery = ref("");
const queryTimeout = ref(null);
const mapboxSearchResults = ref(null);
const searchError = ref(null);

const getSearchResults = () => {
  clearTimeout(queryTimeout.value);
  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== "") {
      try {
        const result = await axios.get(
          `https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`
        );
        mapboxSearchResults.value = result.data.features;
      } catch {
        searchError.value = true;
      }

      return;
    }
    mapboxSearchResults.value = null;
  }, 300);
};
</script>

<style lang="scss" scoped></style>