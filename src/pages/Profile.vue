<script setup>
import { onMounted, ref } from 'vue'
import axios from 'axios'

import CardList from '../components/CardList.vue'

const orders = ref([])

onMounted(async () => {
  try {
    const { data } = await axios.get('https://3b07d63039234082.mokky.dev/orders')
    orders.value = data.flatMap((item) => item.items)
    console.log(orders.value)
  } catch (err) {
    console.log(err)
  }
})
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Мои покупки</h2>

  <CardList :items="orders" isFavorites />
</template>
