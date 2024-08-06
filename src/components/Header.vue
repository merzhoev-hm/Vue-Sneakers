<script setup>
const emit = defineEmits(['openDrawer'])

defineProps({
  totalPrice: Number
})
</script>

<template>
  <header
    class="flex flex-col gap-4 items-center lg:flex-row justify-between border-b border-slate-300 px-10 py-8"
  >
    <RouterLink to="/">
      <div class="flex flex-col text-center sm:flex-row sm:text-left items-center gap-4">
        <img class="w-10" src="/logo.png" alt="logo" />
        <div>
          <h2 class="text-xl font-bold uppercase">Vue Sneakers</h2>
          <p class="text-slate-400">Магазин лучших кроссовок</p>
        </div>
      </div>
    </RouterLink>

    <ul class="flex items-center gap-10">
      <li
        @click="emit('openDrawer')"
        class="flex gap-3 text-gray-500 hover:text-black cursor-pointer"
      >
        <img src="/cart.svg" alt="cart" />
        <b class="hidden sm:block">{{ totalPrice }} руб.</b>
      </li>
      <RouterLink to="/favorites">
        <li class="flex gap-3 text-gray-500 hover:text-black cursor-pointer">
          <img src="/heart.svg" alt="cart" />
          <span class="hidden sm:block">Закладки</span>
        </li>
      </RouterLink>
      <RouterLink to="/profile">
        <li class="flex gap-3 text-gray-500 hover:text-black cursor-pointer">
          <img src="/profile.svg" alt="cart" />
          <span class="hidden sm:block">Профиль</span>
        </li>
      </RouterLink>
    </ul>
  </header>
</template>
