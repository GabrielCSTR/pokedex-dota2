<script setup>
import { onMounted, reactive, ref } from "vue";
import PokeCardVue  from "./components/PokeCard.vue"
import axios from "axios"

const heroStatsData = reactive({
  responseData: null,
  loading: true,
  error: null
})

const fetchApi = async () => {
  try {
    const { data } = await axios.get('https://api.opendota.com/api/heroStats');
    heroStatsData.responseData = data;
    heroStatsData.loading = false;
  } catch (error) {
    heroStatsData.error = error;
    console.log("ERRO:", error.message);
  }
}
const currentHero = ref();
const currentIndex = ref(0);

onMounted(async () => {
  await fetchApi();
  currentHero.value = heroStatsData?.responseData[currentIndex.value]
})

const nextHero = () => {
  currentIndex.value = currentIndex.value + 1;
  currentHero.value =  heroStatsData?.responseData[currentIndex.value]
}
</script>

<template>

  <main class="h-screen flex flex-col items-center justify-center ">
      <PokeCardVue v-if="heroStatsData?.responseData" :heroData="currentHero" />
      <button class="button-primary bg-green-500" @click="nextHero">Next</button>
  </main>

</template>

<style scoped>

</style>
