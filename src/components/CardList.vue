<script setup>
import Card from './Card.vue'

defineProps({
  items: {
    type: Array,
    required: true
  },
  isFavorites: Boolean,
  currentPage: Number,
  totalPage: Number
})

const emit = defineEmits(['addToFavorite', 'onClickAdd', 'nextPage', 'prevPage'])
</script>

<template>
  <div class="grid grid-cols-4 gap-5" v-auto-animate>
    <Card
      v-for="item in items"
      :key="item.id"
      :title="item.title"
      :imageUrl="item.imageUrl"
      :price="item.price"
      :onClickAdd="isFavorites ? null : () => emit('onClickAdd', item)"
      :onClickFavorite="isFavorites ? null : () => emit('addToFavorite', item)"
      :isFavorite="item.isFavorite"
      :isAdded="item.isAdded"
    />
  </div>
  <div v-if="!isFavorites" class="flex justify-center mt-10">
    <button
      class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 w-20 rounded"
      @click="() => emit('prevPage')"
      :disabled="currentPage === 1"
    >
      Назад
    </button>
    <button
      class="bg-green-500 hover:bg-green-700 text-white font-bold py-2 w-20 rounded ml-4"
      @click="() => emit('nextPage')"
      :disabled="currentPage === totalPage"
    >
      Далее
    </button>
  </div>
</template>
