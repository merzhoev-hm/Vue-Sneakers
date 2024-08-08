<script setup>
import { reactive, watch, ref, onMounted } from 'vue'
import axios from 'axios'
import debounce from 'lodash.debounce'
import { inject } from 'vue'
import CardList from '../components/CardList.vue'

const items = ref([])

const { cart, addToCart, removeFromCart } = inject('cart')

const filters = reactive({
  sortBy: 'name',
  searchQuery: ''
})

const onClickAddPlus = (item) => {
  try {
    if (!item.isAdded) {
      addToCart(item)
    } else {
      removeFromCart(item)
    }
  } catch (err) {
    console.log(err)
  }
}

const onChangeselect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = debounce((event) => {
  filters.searchQuery = event.target.value
}, 300)

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        item_id: item.id
      }

      item.isFavorite = true

      const { data } = await axios.post('https://3b07d63039234082.mokky.dev/favorites', obj)

      item.favoriteId = data.id
    } else {
      item.isFavorite = false

      await axios.delete(`https://3b07d63039234082.mokky.dev/favorites/${item.favoriteId}`)

      item.favoriteId = null
    }
  } catch (err) {
    console.log(err)
  }
}

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get('https://3b07d63039234082.mokky.dev/favorites')
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.item_id === item.id)
      if (!favorite) {
        return item
      }
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id
      }
    })
  } catch (err) {
    console.log(err)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const { data } = await axios.get('https://3b07d63039234082.mokky.dev/items', {
      params
    })
    items.value = data.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.log(err)
  } finally {
    fetchFavorites()
    items.value = items.value.map((item) => ({
      ...item,
      isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
    }))
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchItems()
  await fetchFavorites()

  items.value.forEach(
    (item) => (item.isAdded = cart.value.some((cartItem) => cartItem.id === item.id))
  )
})

watch(filters, fetchItems)
watch(cart, () => {
  items.value.forEach((item) => (item.isAdded = false))
})
</script>

<template>
  <div class="flex flex-col md:flex-row justify-between items-center">
    <h2 class="text-2xl sm:text-3xl font-bold mb-6 md:mb-8">Все кроссовки</h2>
    <div class="flex flex-col gap-4 lg:flex-row sm:max-w-fit">
      <select @change="onChangeselect" class="py-2 px-3 border rounded-md outline-none max-w-56">
        <option value="name">По названию</option>
        <option value="price">Сначала дешевые</option>
        <option value="-price">Сначала дорогие</option>
      </select>

      <div class="relative">
        <img src="/search.svg" alt="search" class="absolute left-3 top-3" />
        <input
          @input="onChangeSearchInput"
          class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400 max-w-56"
          type="text"
          placeholder="Поиск..."
        />
      </div>
    </div>
  </div>

  <div>
    <CardList
      @addToFavorite="addToFavorite"
      @addToCart="onClickAddPlus"
      :items="items"
      class="mt-10"
    />
  </div>
</template>
