<script setup>
import axios from 'axios'
import { computed, ref, inject } from 'vue'

import DrawerHead from './DrawerHead.vue'
import CartItemList from './CartItemList.vue'
import infoBlock from './infoBlock.vue'

const props = defineProps({
  totalPrice: Number,
  vatPrice: Number
})

const isCreatingOrder = ref(false)
const orderId = ref(null)

const { cart } = inject('cart')

const createOrder = async () => {
  try {
    isCreatingOrder.value = true
    const { data } = await axios.post('https://3b07d63039234082.mokky.dev/orders', {
      items: cart.value,
      totalPrice: props.totalPrice
    })

    cart.value = []

    orderId.value = data.id
    return data
  } catch (err) {
    console.log(err)
  } finally {
    isCreatingOrder.value = false
  }
}

const buttonDisabled = computed(() =>
  props.isCreatingOrder ? true : props.totalPrice ? false : true
)
</script>

<template>
  <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70"></div>
  <div class="bg-white max-w-96 h-full fixed right-0 top-0 z-20 p-8">
    <DrawerHead />

    <div v-if="!totalPrice" class="flex h-full items-center">
      <infoBlock
        v-if="!totalPrice && !orderId"
        imageUrl="/package-icon.png"
        title="Корзина пустая"
        description="Добавьте хотя-бы одну пару кроссовок, что-бы сделать заказ"
      />
      <infoBlock
        v-if="orderId"
        imageUrl="/order-success-icon.png"
        title="Заказ оформлен"
        :description="`Ваш заказ #${orderId} оформлен скоро будет передан курьерской доставке`"
      />
    </div>

    <CartItemList />

    <div class="flex flex-col gap-5 my-6">
      <div class="flex gap-2">
        <span>Итого</span>
        <div class="border-b border-dashed"></div>
        <b>{{ totalPrice }} руб.</b>
      </div>

      <div class="flex gap-2">
        <span>Налог 5%:</span>
        <div class="border-b border-dashed flex-1"></div>
        <b>{{ vatPrice }} руб.</b>
      </div>

      <button
        :disabled="buttonDisabled"
        @click="createOrder"
        class="mt-4 bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700 transition disabled:bg-slate-300"
      >
        Оформить заказ
      </button>
    </div>
  </div>
</template>
