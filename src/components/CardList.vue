<script setup>
import Card from './Card.vue'
</script>

<template>
  <div class="grid grid-cols-4 gap-5">
    <Card
      title="Мужские кроссовки Nike Blazer"
      imageUrl="/sneakers/sneakers-1.jpg"
      :price="1205"
      :isFavorite="false"
      :isAdded="true"
    />
    <Card />
    <Card />
  </div>
</template>
