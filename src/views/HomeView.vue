<template>
  <!-- container -->
  <section id="home" class="container text-white">
    <div class="p-4 mb-8 relative">
      <input type="text" placeholder="Search for a city or state" v-model="search" ref="searchQuery"
        @input="getSearchQueryResult"
        class="py-2 px-1 w-full bg-transparent border-b focus:border-weather-secondary focus:outline-none focus:shadow-[0px_1px_0_0_#004E71]">

      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]"
        v-if="mapboxSearchResults">
        <p class="px-1 text-warning" v-if="searchError">Sorry, something went wrong please try again later</p>
        <p class="px-1 text-warning" v-if="!searchError && mapboxSearchResults.length === 0">No result match your query,
          try a
          different term</p>
        <template v-else>
          <li class="py-2 cursor-pointer" v-for="searchResult in mapboxSearchResults" :key="searchResult.id"
            @click="previewCity(searchResult)">
            {{ searchResult.place_name }}
          </li>
        </template>

      </ul>
    </div>



  </section>
</template>

<script>

import router from '@/router';
import axios from 'axios'

export default {
  data() {
    return {
      mapboxAPIkey: "pk.eyJ1IjoiYmxhd2lkcmlzIiwiYSI6ImNsaHczMHZnZzBmdGUzaHJ3YjRxYXc4NDIifQ.dK-S5573wS-0Yr4Umh10Wg",
      queryTimeout: null,
      mapboxSearchResults: null,
      searchError: null,
      search: this.$refs.searchQuery
    }
  },

  methods: {
    getSearchQueryResult() {

      clearTimeout(this.queryTimeout);

      this.queryTimeout = setTimeout(async () => {
        if (this.search !== null) {

          try {
            const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${this.search}.json?access_token=${this.mapboxAPIkey}&types=place`);

            this.mapboxSearchResults = result.data.features;

            // console.log(this.mapboxSearchResults);

          } catch {
            this.searchError = true;
          }

        }
        // this.mapboxSearchResults = null;
        return;
      }, 300);
    },

    previewCity(item) {
      this.search = item.place_name

      const [city, state] = item.place_name.split(',')

      console.log(city, state, item)
      router.push({
        name: 'cityView',
        params: { city: city.replaceAll(" ","-"), state: state.replaceAll(" ", "")},
        query:{
          lat: item.geometry.coordinates[0],
          lng: item.geometry.coordinates[1],
          preview:true
        }
      })
    }
  }
}
</script>



<!-- <script setup>

import { ref } from 'vue'
import axios from 'axios'

const mapboxAPIkey = 'pk.eyJ1IjoiYmxhd2lkcmlzIiwiYSI6ImNsaHczMHZnZzBmdGUzaHJ3YjRxYXc4NDIifQ.dK-S5573wS-0Yr4Umh10Wg'

const searchQuery = ref(null)
const queryTimeout = ref(null)
const mapboxResults = ref(null)
const searchErr = ref(null)

const previewCity = (item) => {
  console.log(item)
}

const getSearchQuery = () => {

  clearTimeout(queryTimeout.value);

  queryTimeout.value = setTimeout(async () => {
    if (searchQuery.value !== null) {

      try {
        const result = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIkey}&types=place`);

        mapboxResults.value = result.data.features;

        // console.log(mapboxResults.value);
      } catch {
        searchErr.value = true;
      }

    }
    mapboxResults.value = null;
    return;
  }, 300);
}

</script> -->

