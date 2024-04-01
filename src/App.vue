<script setup>
import { onMounted, reactive, ref } from "vue";
import PokeCardVue from "./components/PokeCard.vue"
import axios from "axios"

const heroStatsData = reactive({
  responseData: null,
  responseHeroSkills: null,
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

const fetchHeroSkill = async (heroId) => {
  try {
    const data = await axios.get(`https://www.dota2.com/datafeed/herodata?language=brazilian&hero_id=${heroId}`);
    heroStatsData.responseHeroSkills = data?.data?.result?.data?.heroes[0];
    heroStatsData.loading = false;
    console.log("HEROS DATA", heroStatsData);
  } catch (error) {
    heroStatsData.error = error;
    console.log("ERRO:", error.message);
  }
}

const currentHero = ref();
const currentIndex = ref(0);

onMounted(async () => {
  await fetchApi();
  await fetchHeroSkill(currentIndex.value + 1);
  currentHero.value = {...heroStatsData?.responseData[currentIndex.value], heroSkills: { ...heroStatsData?.responseHeroSkills } };
  console.log("CURRENT HERO", currentHero.value);
})

const nextHero = async () => {
  currentIndex.value = currentIndex.value + 1;
  await fetchHeroSkill(currentIndex.value + 1);
  currentHero.value = {...heroStatsData?.responseData[currentIndex.value], heroSkills: { ...heroStatsData?.responseHeroSkills }};
}

const prevHero = async () => {
  if (currentIndex.value === 0) return;
  currentIndex.value = currentIndex.value - 1;
  await fetchHeroSkill(currentIndex.value - 1);
  currentHero.value = {...heroStatsData?.responseData[currentIndex.value], heroSkills: { ...heroStatsData?.responseHeroSkills }};
}

</script>

<template>

  <main class="h-screen flex flex-col items-center justify-center bg-yellow-300">

    <PokeCardVue v-if="heroStatsData?.responseData" :heroData="currentHero" />

    <div v-if="heroStatsData?.responseData" class="relative w-[800px] min-w-[800px] max-w-[800px]">
      <div class="relative top-[-280px] left-[210px]">
        <button @click="prevHero"
          class="relative inline-flex items-center justify-center p-0.5 mb-2 me-2 overflow-hidden text-sm font-medium text-gray-900 rounded-lg group bg-gradient-to-br from-slate-600 to-slate-500 group-hover:from-slate-600 group-hover:to-slate-500 hover:text-white dark:text-white focus:ring-4 focus:outline-none focus:ring-slate-300 dark:focus:ring-slate-800">
          <span
            class="relative px-5 py-2.5 transition-all ease-in duration-75 bg-white dark:bg-gray-900 rounded-md group-hover:bg-opacity-0">
            PREV< </span>
        </button>
        <button @click="nextHero"
          class="relative inline-flex items-center justify-center p-0.5 mb-2 me-2 overflow-hidden text-sm font-medium text-gray-900 rounded-lg group bg-gradient-to-br from-slate-600 to-slate-500 group-hover:from-slate-600 group-hover:to-slate-500 hover:text-white dark:text-white focus:ring-4 focus:outline-none focus:ring-slate-300 dark:focus:ring-slate-800">
          <span
            class="relative px-5 py-2.5 transition-all ease-in duration-75 bg-white dark:bg-gray-900 rounded-md group-hover:bg-opacity-0">
            NEXT>
          </span>
        </button>
      </div>
    </div>
  </main>

</template>

<style scoped></style>
