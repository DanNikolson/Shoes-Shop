<script setup>
import { onMounted, inject, watch } from 'vue'
import axios from 'axios'
import CardList from '@/components/CardList.vue'
import InfoBlock from '@/components/InfoBlock.vue'

const { items, cart, addToFavourite, fetchFavourites, onClickAddPlus } = inject('cart')

const fetchDataAndUpdateFavourites = async () => {
  try {
    const { data } = await axios.get('https://a802cbe354cb10b1.mokky.dev/favourites')
    items.value = data.map((item) => ({
      ...item,
      isFavourite: true,
      isActive: true,
      favouriteId: item.id,
      isAdded: false
    }))
  } catch (err) {
    console.error(err)
  }
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchDataAndUpdateFavourites()
  await fetchFavourites()
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.parentId)
  }))
})

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Мои закладки</h2>
  <div class="mt-10" v-auto-animate>
    <CardList :items="items" @addToFavourite="addToFavourite" @addToCart="onClickAddPlus" />
    <InfoBlock
      v-if="items.length === 0"
      title="Закладок нет :("
      :description="`Вы ничего не добавляли в закладки`"
      image-url="/emoji-1.png"
    />
  </div>
</template>
