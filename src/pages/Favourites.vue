<script setup>
import { ref, onMounted, inject } from 'vue'
import axios from 'axios'
import CardList from '@/components/CardList.vue'

const { onClickAddPlus, addToFavourite } = inject('cart')

const favourites = ref([])

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

onMounted(async () => {
  await fetchDataAndUpdateFavourites()
})
</script>

<template>
  <h1>Мои закладки</h1>
  <div class="mt-10">
    <CardList :items="favourites" @addToFavourite="onAddToFavourite" @addToCart="onClickAddPlus" />
  </div>
</template>
