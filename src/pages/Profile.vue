<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

import CardList from '../components/CardList.vue'
import infoBlock from '../components/infoBlock.vue'

const orders = ref([])
const isOrder = ref(true)

onMounted(async () => {
  try {
    const { data } = await axios.get('https://3b07d63039234082.mokky.dev/orders')
    orders.value = data.flatMap((item) => item.items)
    if (orders.value.length == 0) {
      isOrder.value = false
    }
  } catch (err) {
    console.log(err)
  }
})
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Мои заказы</h2>

  <div class="min-h-96">
    <CardList v-if="isOrder" :items="orders" isFavorites />
    <div class="flex flex-col items-center gap-8" v-if="!isOrder">
      <infoBlock
        imageUrl="/emoji-2.png"
        title="У вас нет заказов"
        description="Вы нищеброд? Оформите хотя бы один заказ."
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
