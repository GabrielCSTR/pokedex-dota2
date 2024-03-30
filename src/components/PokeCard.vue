<script setup lang="ts">
import { computed, defineProps, onMounted, reactive, ref, watch } from 'vue'
import pokeDex from '../assets/pokedex.png'
import heroMedia from './heroMedia.vue';

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
    handleMedia(newValue.heroData?.name)
}, {deep: true })


const handleMedia = (hero_name: string) =>{
    let videoHeroName = hero_name.replace("npc_dota_hero_", "");
    heroImage.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.png` : '';
    heroVideo.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.webm` : '';
}


let videoHeroName = hero?.heroData?.name.replace("npc_dota_hero_", "");
    heroImage.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.png` : '';
    heroVideo.value = videoHeroName ? `https://cdn.cloudflare.steamstatic.com/apps/dota2/videos/dota_react/heroes/renders/${videoHeroName}.webm` : '';


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

    </div>



</template>

<style scoped></style>
