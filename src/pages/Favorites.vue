<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

import CardList from '../components/CardList.vue'
import infoBlock from '../components/infoBlock.vue'

const favorites = ref([])
const isFavorite = ref(true)

onMounted(async () => {
  try {
    const { data } = await axios.get(
      'https://3b07d63039234082.mokky.dev/favorites?_relations=items'
    )
    favorites.value = data.map((obj) => obj.item)
    if (favorites.value.length == 0) {
      isFavorite.value = false
    }
  } catch (err) {
    console.log(err)
  }
})
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Мои закладки</h2>

  <div class="min-h-96">
    <CardList v-if="isFavorite" :items="favorites" isFavorites />
    <div class="flex flex-col items-center gap-8" v-if="!isFavorite">
      <infoBlock
        imageUrl="/emoji-1.png"
        title="Закладок нет :("
        description="Вы ничего не добавляли в закладки"
      />
      <RouterLink to="/">
        <button class="bg-lime-400 text-white py-4 px-14 rounded-2xl flex items-center gap-5">
          <svg
            class="rotate-180"
            width="16"
            height="14"
            viewBox="0 0 16 14"
            fill="none"
            xmlns="http://www.w3.org/2000/svg"
          >
            <path
              d="M1 7H14.7143"
              stroke="white"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
            <path
              d="M8.71436 1L14.7144 7L8.71436 13"
              stroke="white"
              stroke-width="2"
              stroke-linecap="round"
              stroke-linejoin="round"
            />
          </svg>
          <p>Вернуться назад</p>
        </button>
      </RouterLink>
    </div>
  </div>
</template>
