<script setup>
import { ref, onMounted, inject, watch } from 'vue'
import axios from 'axios'
import CardList from '@/components/CardList.vue'

const { onClickAddPlus, addToFavourite, cart } = inject('cart')

let favourites = ref([])
const cartInFavourite = JSON.parse(localStorage.getItem('cart')).filter(
  (item) => item.isFavourite === true
)

const fetchDataAndUpdateFavourites = async () => {
  try {
    const { data } = await axios.get('https://a802cbe354cb10b1.mokky.dev/favourites')
    favourites.value = data.map((item) => ({
      ...item,
      isFavourite: true,
      isActive: true,
      favouriteId: item.id
    }))
  } catch (err) {
    console.error(err)
  }
}

const onAddToFavourite = async (item) => {
  await addToFavourite(item)
  fetchDataAndUpdateFavourites()
}

const onAddToCart = (item) => {
  item.id = item.parentId
  item.parentId = null
  onClickAddPlus(item)
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
console.log(favourites)
console.log(cart)

onMounted(async () => {
  await fetchDataAndUpdateFavourites()
  if (cartInFavourite) {
    cartInFavourite.forEach((cartItem) => {
      const index = favourites.value.findIndex((favItem) => favItem.parentId === cartItem.id)
      if (index !== -1) {
        favourites.value.splice(index, 1)
      }
      favourites.value.push(cartItem)
    })
  }
})
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Мои закладки</h2>
  <div class="mt-10" v-auto-animate>
    <CardList :items="favourites" @addToFavourite="onAddToFavourite" @addToCart="onAddToCart" />
  </div>
</template>
