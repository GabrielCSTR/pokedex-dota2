<script setup lang="ts">
import {  defineProps, reactive, ref, watch } from 'vue'
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

watch(() => propHero, (newValue, oldValue) => {
    handleMedia(newValue.heroData?.name);
    handleRadar(newValue.heroData);
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


let videoHeroName = hero?.heroData?.name.replace("npc_dota_hero_", "");
heroImage.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.png` : '';
heroVideo.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.webm` : '';


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

        <div class="absolute top-[500px] left-[40px]">
            <div class="flex flex-row items-center gap-2">
                <h1 class="text-lg font-bold">{{ propHero.heroData?.localized_name }} -</h1>
                <img class="h-5 w-5" :src="`https://cdn.cloudflare.steamstatic.com/${propHero?.heroData?.icon}`" />
            </div>
        </div>

        <div class="absolute max-h-80 top-[220px] left-[440px]">
            <Radar :data="chartData" :options="options" :key="propHero?.heroData?.id" />
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
