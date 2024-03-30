<script setup lang="ts">
import { reactive } from "vue";

const mediaData = defineProps({
    heroImage: {
        type: String,
        required: true
    },
    heroVideo: {
        type: String,
        required: true
    }
})

const mediaHero = reactive(mediaData);

</script>

<template>
    <video class="absolute w-[280px] top-[70px] left-[50px]" :poster="mediaHero.heroImage" autoplay="true"
        preload="auto" loop="" playsinline="">
        <source type="video/webm" :src="mediaHero.heroVideo">
        <img :src="mediaHero.heroImage">
    </video>
</template>