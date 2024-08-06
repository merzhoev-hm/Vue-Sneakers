<script setup>
defineProps({
  id: Number,
  imageUrl: String,
  title: String,
  price: Number
})

const emit = defineEmits(['onClickRemove'])
</script>

<template>
  <div class="border border-slate-100 p-4 rounded-xl flex items-center gap-5">
    <img class="w-16 h-16" :src="imageUrl" alt="Sneaker" />

    <div class="flex flex-col gap-2 flex-1">
      <p>{{ title }}</p>

      <div class="flex justify-between">
        <b>{{ price }} руб.</b>
        <img
          @click="emit('onClickRemove')"
          src="/close.svg"
          alt="close"
          class="opacity-40 hover:opacity-100 cursor-pointer transition"
        />
      </div>
    </div>
  </div>
</template>
