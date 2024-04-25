<script setup>
import { onMounted, inject, watch, ref } from 'vue'
import axios from 'axios'
import CardList from '@/components/CardList.vue'
import InfoBlock from '@/components/InfoBlock.vue'

const { items, cart, addToFavourite, fetchFavourites, onClickAddPlus } = inject('cart')

const showInfo = ref(false)
const fetchDataAndUpdateFavourites = async () => {
  try {
    const { data } = await axios.get('https://a802cbe354cb10b1.mokky.dev/favourites')
    items.value = data.map((item) => ({
      ...item,
      isFavourite: true,
      isActive: true,
      favouriteId: item.id
    }))
    updateItems()
  } catch (err) {
    console.error(err)
  }
}

function updateItems() {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.parentId)
  }))
}

onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchDataAndUpdateFavourites()
  await fetchFavourites()
  updateItems()
  setTimeout(() => {
    showInfo.value = true
  }, 500)
})

const onAddToCart = async (item) => {
  item.id = item.parentId
  delete item.parentId
  await onClickAddPlus(item)
}

const onAddToFavourite = async (item) => {
  item.favouriteId = item.id
  await addToFavourite(item)
  await fetchDataAndUpdateFavourites()
}

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
    <CardList :items="items" @addToFavourite="onAddToFavourite" @addToCart="onAddToCart" />

    <InfoBlock
      v-if="items.length === 0 && showInfo"
      title="Закладок нет :("
      :description="`Вы ничего не добавляли в закладки`"
      image-url="/emoji-1.png"
    />
  </div>
</template>
