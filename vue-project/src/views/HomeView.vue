<template>
  <main class="container text-white">
    <div class="pt-4 mb-8 relative">
      <input v-model="searchQuery" @input="getSearchResults" type="text" placeholder="Search for a city or state" class="py-2 px-1 w-full bg-transparent border-b 
      focus:border-weather-secondary focus:outline-none">
      <ul class="absolute bg-weather-secondary text-white w-full shadow-md py-2 px-1 top-[66px]" v-if="mapBoxSearchResults">
        <p v-if="searchError">Sorry, something went wrong,please try again.</p>
        <p v-if="!searchError && mapBoxSearchResults.length == 0">
          No results match your query, try a different term.
        </p>
        <li v-for="searchResult in mapBoxSearchResults" :key="searchResult.id" @click="previewCity(searchResult)" class="py-2 cursor-pointer">
          {{ searchResult.place_name }}
        </li>
      </ul>
    </div>
  </main>
</template>

<script setup>
import { ref } from "vue"
import axios from "axios"
import { useRouter } from "vue-router" 

const router = useRouter();
const previewCity = (searchResult) => {
  const [city,state] = searchResult.place_name.split(",");
  router.push({
    name: "cityView",
    params: {state: state.replaceAll(" ",""),city:city},
    query: {
      lat: searchResult.geometry.coordinates[1],
      lng: searchResult.geometry.coordinates[0],
      preview: true,
    }
  })
}

const mapboxAPIKey =
  "pk.eyJ1Ijoiam9obmtvbWFybmlja2kiLCJhIjoiY2t5NjFzODZvMHJkaDJ1bWx6OGVieGxreSJ9.IpojdT3U3NENknF6_WhR2Q";

  const searchQuery = ref("");
  const queryTimeOut = ref(null);
  const mapBoxSearchResults = ref(null);
  const searchError = ref(null);

  const getSearchResults = () => {
    clearTimeout(queryTimeOut.value);
    queryTimeOut.value = setTimeout(async () => {
      if(searchQuery.value !== ""){
        try{
          const results = await axios.get(`https://api.mapbox.com/geocoding/v5/mapbox.places/${searchQuery.value}.json?access_token=${mapboxAPIKey}&types=place`)
          mapBoxSearchResults.value = results.data.features;
          return;
        }catch{
          searchError.value = true;
        }
      }
      mapBoxSearchResults.value = null;
    },300);
  }
</script>
