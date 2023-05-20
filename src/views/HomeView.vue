<template>
  <!-- container -->
  <section id="home" class="container text-white">
    <div class="p-4 mb-8 relative">
      <input type="text" placeholder="Search for a city or state" v-model="searchQuery" @input="getSearchQuery"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapboxResults">
        <p class="px-1 text-warning" v-if="searchErr">Sorry, something went wrong please try again later</p>
        <p class="px-1 text-warning" v-if="!searchErr && mapboxResults.length === 0">No result match your query, try a
          different term</p>
        <template v-else>
          <li class="py-2 cursor-pointer" v-for="searchResult in mapboxResults" :key="searchResult.id">
            {{ searchResult.place_name }}
          </li>
        </template>

      </ul>
    </div>



  </section>
</template>


<script setup>

import { ref } from 'vue'
import axios from 'axios'

const mapboxAPIkey = 'pk.eyJ1IjoiYmxhd2lkcmlzIiwiYSI6ImNsaHczMHZnZzBmdGUzaHJ3YjRxYXc4NDIifQ.dK-S5573wS-0Yr4Umh10Wg'

const searchQuery = ref(null)
const queryTimeout = ref(null)
const mapboxResults = ref(null)
const searchErr = ref(null)

const getSearchQuery = () => {

  clearTimeout(queryTimeout.value);

  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== null) {

      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIkey}&types=place`)

        mapboxResults.value = result.data.features;

      } catch {
        searchErr.value = true
        console.log(searchErr)
      }

    }
    mapboxResults.value = null;
    return;
  }, 300);
}

</script>
