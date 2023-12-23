<script setup>
import axios from 'axios'
import debounce from 'lodash.debounce'
import { ref, watch, onMounted, inject, reactive } from 'vue'
import CardList from '../components/CardList.vue'

const { cart, addToCart, removeFromCart } = inject('cart')
const items = ref([])
const page = ref(1)
const totalPage = ref(null)

const filters = reactive({
  sortBy: 'title',
  searchQuery: '',
  page: 1,
  limit: 4
})

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const nextPage = () => {
  if (page.value < totalPage.value) {
    page.value++
    fetchFavorites()
    fetchItems()
    fetchFavorites()
  }
}

const prevPage = () => {
  if (page.value > 1) {
    page.value--
    fetchFavorites()
    fetchItems()
    fetchFavorites()
  }
}

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = debounce((event) => {
  filters.searchQuery = event.target.value
}, 300)

const fetchFavorites = async () => {
  try {
    const { data: favorites } = await axios.get(`https://aec5457cfecb56b0.mokky.dev/favorites`)
    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.parentId === item.id)

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
      sortBy: filters.sortBy,
      page: page.value,
      limit: filters.limit
    }
    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
    const { data } = await axios.get(`https://aec5457cfecb56b0.mokky.dev/items`, {
      params
    })
    totalPage.value = data.meta.total_pages
    items.value = data.items.map((obj) => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.log(err)
  }
}

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = { parentId: item.id, item }
      const { data } = await axios.post(`https://aec5457cfecb56b0.mokky.dev/favorites`, obj)
      item.isFavorite = true
      item.favoriteId = data.id
    } else {
      await axios.delete(`https://aec5457cfecb56b0.mokky.dev/favorites/${item.favoriteId}`)
      item.isFavorite = false
      item.favoriteId = null
    }
  } catch (err) {
    console.log(err)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []
  await fetchItems()
  await fetchFavorites()
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})

watch(cart, () => { 
  items.value.forEach(item => {
    const itemInCart = cart.value.find(i => i.id === item.id)
    if(itemInCart) {
      item.isAdded = true 
    } 
  })
})


watch(filters, fetchItems)
</script>

<template>
  <div class="flex items-center justify-between">
    <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
    <div class="flex gap-4">
      <select
        @change="onChangeSelect"
        class="py-2 px-3 border rounded-md outline-none cursor-pointer"
      >
        <option value="name">По названию</option>
        <option value="price">По цене(дешёвые)</option>
        <option value="-price">По цене(дорогие)</option>
      </select>
      <div class="relative">
        <img class="absolute left-4 top-3" src="/search.svg" alt="search" />
        <input
          @input="onChangeSearchInput"
          class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
          placeholder="Поиск..."
          type="text"
        />
      </div>
    </div>
  </div>
  <div class="mt-10">
    <CardList
      :items="items"
      @addToFavorite="addToFavorite"
      @onClickAdd="onClickAddPlus"
      @nextPage="nextPage"
      @prevPage="prevPage"
      :currentPage="page"
      :totalPage="totalPage"
    />
  </div>
</template>
