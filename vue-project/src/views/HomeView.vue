<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for a city or state" class="py-2 px-1 w-full bg-transparent border-b 
      focus:border-weather-secondary focus:outline-none">
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapBoxSearchResults">
        <li v-for="searchResult in mapBoxSearchResults" :key="searchResult.id" class="py-2 cursor-pointer">
          {{ searchResult.place_name }}
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue"
import axios from "axios"

const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";

  const searchQuery = ref("");
  const queryTimeOut = ref(null);
  const mapBoxSearchResults = ref(null);

  const getSearchResults = () => {
    clearTimeout(queryTimeOut.value);
    queryTimeOut.value = setTimeout(async () => {
      if(searchQuery.value !== ""){
        const results = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`)
        mapBoxSearchResults.value = results.data.features;
        return;
      }
      mapBoxSearchResults.value = null;
    },300);
  }
</script>
