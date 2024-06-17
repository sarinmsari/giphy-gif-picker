<script setup>
import {ref,onMounted} from 'vue'
import { renderGrid } from '@giphy/js-components'
import { GiphyFetch } from '@giphy/js-fetch-api'
import { debounce } from 'lodash';

const emit = defineEmits(['handleGifSelection'])

let gifs = ref(null),
    searchTerm = ref(''),
    grid = null;

//const gf = new GiphyFetch('SpwaDr1NYWlPudRrU16vcGzL5g3vFSB5')
const gf = new GiphyFetch(import.meta.env.VITE_GIPHY_SDK_KEY)

onMounted(() => {
    grid = makeGrid(gifs.value)
})

const fetchGifs = (offset) => {
    if (searchTerm.value) {
        return gf.search(searchTerm.value, { offset, limit: 25 })
    }
    return gf.trending({ offset, limit: 25 })
}
let width = 226;
const makeGrid = (targetEl) => {
    const render = () => {
        return renderGrid(
            {
                width: width,
                fetchGifs,
                columns: width < 500 ? 2 : 3,
                gutter: 6,
                noLink: true,
                hideAttribution: true,
                onGifClick,
            },
            targetEl
        )
    }
    const remove = render()
    return {
        remove: () => {
            remove()
        },
    }
}

const onGifClick = (gif, e) => {
    e.preventDefault();
    emit('handleGifSelection', gif.images.fixed_height.url);
}

const handleGifSearch = debounce(() => {
    clearGridAndFetchGifs();
},500)

const handleTrendingClick = () => {
    searchTerm.value = '';
    clearGridAndFetchGifs();
}

const clearGridAndFetchGifs = () => {
    grid.remove();
    grid=makeGrid(gifs.value)
}

</script>

<template>
    <div class="flex flex-col items-center justify-center w-[280px] h-[350px] bg-white shadow-lg rounded-2xl border p-4">
        <div class="flex items-center justify-between w-full">
            <input
                type="text"
                v-model="searchTerm"
                @input="handleGifSearch"
                class="w-full text-xl p-2 border rounded-xl"
                placeholder="Search gif"/>
            <span @click="handleTrendingClick" class=" ml-2 text-xl p-2 bg-white border flex items-center justify-center rounded-xl hover:bg-gray-100 cursor-pointer">🔥</span>
        </div>

        <div class="flex flex-wrap items-center justify-center w-full h-full overflow-y-auto">
            <div class="mt-2" ref="gifs"/>
        </div>
    </div>
</template>