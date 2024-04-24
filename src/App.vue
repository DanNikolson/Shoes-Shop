<script setup>
import { computed, provide, ref, watch } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import Drawer from './components/Drawer.vue'

/* Корзина (START) */
const cart = ref([])
const items = ref([])

const drawerOpen = ref(false)

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0))
const vatPrice = computed(() => Math.round(totalPrice.value * 0.05))

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item)
  } else {
    removeFromCart(item)
  }
}

const addToFavourite = async (item) => {
  try {
    if (!item.isActive) return
    if (!item.isFavourite) {
      const obj = {
        parentId: item.id,
        imageUrl: item.imageUrl,
        title: item.title,
        price: item.price
      }
      item.isFavourite = true
      if (item.isActive) {
        item.isActive = false

        const data = await axios.post(`https://a802cbe354cb10b1.mokky.dev/favourites`, obj)
        item.favouriteId = data.data.id
        if (data.status === 201) {
          item.isActive = true
        }
      }
    } else if (item.isActive) {
      item.isActive = false
      item.isFavourite = false
      const data = await axios.delete(
        `https://a802cbe354cb10b1.mokky.dev/favourites/${item.favouriteId}`
      )

      item.favouriteId = null
      if (data.status === 200) item.isActive = true
    }
  } catch (err) {
    console.log(err)
  }
}

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
  cart.value.splice(cart.value.indexOf(item), 1)
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
/* Корзина (END)*/

const fetchFavourites = async () => {
  try {
    const { data: favourites } = await axios.get(`https://a802cbe354cb10b1.mokky.dev/favourites`)
    items.value = items.value.map((item) => {
      const favourite = favourites.find((favourite) => favourite.parentId === item.id)

      if (!favourite) {
        return item
      }

      return {
        ...item,
        isFavourite: true,
        favouriteId: favourite.id
      }
    })
  } catch (err) {
    console.log(err)
  }
}

provide('cart', {
  cart,
  items,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart,
  onClickAddPlus,
  addToFavourite,
  fetchFavourites
})
</script>

<template>
  <Drawer v-if="drawerOpen" :total-price="totalPrice" :vatPrice="vatPrice" />
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14 overflow-hidden max-w-fit">
    <Header :totalPrice="totalPrice" @openDrawer="openDrawer" />

    <div class="p-10">
      <RouterView />
    </div>
  </div>
</template>
