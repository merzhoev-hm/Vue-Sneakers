<script setup>
import { ref, watch, provide, computed } from 'vue'

import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'

/* Корзина (START) */
const cart = ref([])
const drawerOpen = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100))

const closeDrawer = () => {
  drawerOpen.value = false
  document.body.style.paddingRight = ''
  document.body.classList.remove('overflow-hidden')
}

const openDrawer = () => {
  drawerOpen.value = true
  document.body.style.paddingRight = window.innerWidth - document.documentElement.clientWidth + 'px'
  document.body.classList.add('overflow-hidden')
}

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false
}

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart
})

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  { deep: 'true' }
)
/* Корзина (START) */
</script>

<template>
  <Drawer v-if="drawerOpen" :totalPrice="totalPrice" :vatPrice="vatPrice" />
  <div class="bg-white w-11/12 sm:w-4/5 m-auto rounded-xl shadow-xl my-14">
    <Header :totalPrice="totalPrice" @openDrawer="openDrawer" />

    <div class="p-10">
      <RouterView />
    </div>
  </div>
</template>
