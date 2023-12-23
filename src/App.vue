<script setup>
import { ref, watch, provide, computed } from 'vue'
import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'

const cart = ref([])
const drawerOpen = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const vatPrice = computed(() => Math.round(totalPrice.value * 0.05))

const closeDrawer = () => {
  drawerOpen.value = false
}

const openDrawer = () => {
  drawerOpen.value = true
}

const addToCart = (item) => {
  cart.value.push(item)
  item.isAdded = true
}

const removeFromCart = (item) => {
  cart.value = cart.value.filter((cartItem) => cartItem.id !== item.id)
  item.isAdded = false
}

watch(
  cart,
  () => {
    localStorage.setItem('cart', JSON.stringify(cart.value))
  },
  {
    deep: true
  }
)

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart
})
</script>

<template>
  <div>
    <Drawer v-if="drawerOpen" :totalPrice="totalPrice" :vatPrice="vatPrice" />
    <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
      <Header @openDrawer="openDrawer" :totalPrice="totalPrice" />
      <div class="p-10">
        <router-view></router-view>
      </div>
    </div>
  </div>
</template>
