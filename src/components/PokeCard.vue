<script setup lang="ts">
import { defineProps, reactive, ref, watch } from 'vue'
import pokeDex from '../assets/pokedex.png'
import heroMedia from './heroMedia.vue';
import {
    Chart as ChartJS,
    RadialLinearScale,
    PointElement,
    LineElement,
    Filler,
    Tooltip,
    Legend
} from 'chart.js'
import { Radar } from 'vue-chartjs'

ChartJS.register(
    RadialLinearScale,
    PointElement,
    LineElement,
    Filler,
    Tooltip,
    Legend
)

const propHero = defineProps({
    heroData: {
        type: Object,
        required: true
    }
})
const hero = reactive(propHero);
const heroImage = ref();
const heroVideo = ref();
const heroRoles = ref();
const heroPrimaryAttr = ref();

const heroesAttr =
{
    agi: "https://cdn.cloudflare.steamstatic.com/apps/dota2/images/dota_react/icons/hero_agility.png",
    str: "https://cdn.cloudflare.steamstatic.com/apps/dota2/images/dota_react/icons/hero_strength.png",
    int: "https://cdn.cloudflare.steamstatic.com/apps/dota2/images/dota_react/icons/hero_intelligence.png",
    all: "https://cdn.cloudflare.steamstatic.com/apps/dota2/images/dota_react/icons/hero_universal.png",
}

watch(() => propHero, (newValue, oldValue) => {
    handleMedia(newValue.heroData?.name);
    handleRadar(newValue.heroData);
    handleInfo(newValue.heroData);
}, { deep: true })


const handleMedia = (hero_name: string) => {
    let videoHeroName = hero_name.replace("npc_dota_hero_", "");
    heroImage.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.png` : '';
    heroVideo.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.webm` : '';
}

const handleRadar = (heroStat) => {
    chartData.value.datasets[0].data =
        [
            parseInt(heroStat?.base_health),
            parseInt(heroStat?.base_mana),
            parseInt(heroStat?.attack_range),
            parseInt(heroStat?.base_attack_min),
            parseInt(heroStat?.base_armor),
            parseInt(heroStat?.move_speed),
            parseInt(heroStat?.base_mr)
        ];
}

const handleInfo = (heroStart) => {
    heroRoles.value = heroStart?.roles.join(", ")
}

let videoHeroName = hero?.heroData?.name.replace("npc_dota_hero_", "");
heroImage.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.png` : '';
heroVideo.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.webm` : '';

heroRoles.value = hero.heroData?.roles.join(", ")

const chartData = ref({
    labels: [
        'HEALTH',
        'MANA',
        'ATTACK RANGE',
        'BASE ATTACK',
        'BASE ARMOR',
        'MOVE SPEED',
        'MAGIC RESISTANCE'
    ],
    datasets: [
        {
            label: '',
            backgroundColor: 'rgba(255,99,132,0.2)',
            borderColor: 'rgba(255,99,132,1)',
            pointBackgroundColor: 'rgba(255,99,132,1)',
            pointBorderColor: '#fff',
            pointHoverBackgroundColor: '#fff',
            pointHoverBorderColor: 'rgba(255,99,132,1)',
            data: []
        }
    ]
})

const options = {
    responsive: true,
    maintainAspectRatio: false,
    label: {
        display: false
    },
    scales: {
        r: {
            grid: {
                color: 'gray'
            },
            angleLines: {
                color: 'rgba(240, 240, 240,0.5)',
            },
            pointLabels: {
                color: 'white'
            },
            ticks: {
                color: 'white',
                backdropColor: '#000'
            }
        }
    },

}

</script>

<template>

    <div class="relative w-[800px] min-w-[800px] max-w-[800px]">
        <img class="z-0 w-[1200px] object-cover" :src="pokeDex" />

        <heroMedia :heroImage="heroImage" :heroVideo="heroVideo" :key="propHero?.heroData?.id" />

        <div class="absolute top-[500px] left-[30px]">
            <div class="flex flex-row items-start w-80 dark:hover:bg-gray-700">
                <img class="object-fill rounded-lg ml-1 h-24 w-24"
                    :src="`https://cdn.cloudflare.steamstatic.com/${propHero?.heroData?.img}`" />
                <div class="flex flex-col leading-normal ml-2">
                    <div class="flex flex-row items-start">
                        <h5 class="text-lg font-bold">{{ propHero.heroData?.localized_name }}</h5>
                        <img class="ml-2 h-7 w-7"
                            :src="`https://cdn.cloudflare.steamstatic.com/${propHero?.heroData?.icon}`" />
                        <img class="ml-1 h-7 w-7"
                            :src="heroesAttr[propHero.heroData?.primary_attr]" />
                    </div>
                    <p><span class="text-gray-50">{{ propHero.heroData?.attack_type }}</span> - {{ heroRoles }} </p>
                </div>
            </div>
        </div>

        <div class="absolute top-[432px] left-[25px]">
            <input type="text" id="hero_name"
                class="bg-gray-50 border border-gray-300 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block w-[232px] p-3 dark:bg-gray-700 dark:border-gray-600 dark:placeholder-gray-400 dark:text-white dark:focus:ring-blue-500 dark:focus:border-blue-500">
        </div>

        <div class="absolute max-h-80 top-[100px] left-[440px]">
            <Radar :data="chartData" :options="options" :key="propHero?.heroData?.id" />
        </div>

        <div class="absolute max-h-80 top-[400px] left-[440px]">
            <div class="flex flex-row items-center m-2">
                <div v-for="(skill, index) in propHero?.heroData?.heroSkills?.abilities" :key="index">
                    <img class="w-14"
                        :src="`https://cdn.cloudflare.steamstatic.com/apps/dota2/images/dota_react/abilities/${skill.name}.png`">
                </div>
                </div>
        </div>

    </div>

</template>

<style scoped>
.chart-container {
    position: relative;
    height: 90vh;
}

canvas {
    background: #163355;
}
</style>
