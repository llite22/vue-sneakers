<script setup>
const emit = defineEmits(['onClickRemove'])

defineProps({
  title: String,
  imageUrl: String,
  price: Number
})
</script>

<template>
  <div class="flex items-center border border-slate-100 p-4 rounded-xl gap-4">
    <img width="70" height="70" :src="imageUrl" alt="Sneaker" />
    <div class="flex flex-col flex-1">
      <p>{{ title }}</p>
      <div class="flex justify-between mt-2">
        <b class="flex-1">{{ price }} ₽</b>
        <img
          @click="emit('onClickRemove')"
          class="opacity-40 hover:opacity-100 cursor-pointer transition"
          src="/close.svg"
          alt="close"
        />
      </div>
    </div>
  </div>
</template>
