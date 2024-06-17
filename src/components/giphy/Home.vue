<script setup>
import {ref} from 'vue'
import GifPicker from '@/components/giphy/GifPicker.vue';

let showGifPicker = ref(false);
let gifUrl = ref('');

const handleGifSelection = (gif) => {
    showGifPicker.value = false;
    gifUrl.value = gif;
}

</script>
<template>
    <div class="relative flex flex-col items-center justify-center w-full">

        <GifPicker v-if="showGifPicker"
            @handleGifSelection="handleGifSelection"
            class="absolute text-black bottom-14"/>
        <button @click="showGifPicker = !showGifPicker"
            class="bg-green-700 p-4 rounded-2xl text-white">Pick Gif</button>
        <img v-if="gifUrl"
            :src="gifUrl"
            class="w-40 h-auto rounded-2xl absolute top-20"/>
            
    </div>
</template>