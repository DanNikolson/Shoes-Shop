<script setup>
import { ref, onMounted, watch, inject } from 'vue'
import axios from 'axios'
import CardList from '@/components/CardList.vue'
import InfoBlock from '@/components/InfoBlock.vue'

const { cart, addToFavourite, fetchFavourites, onClickAddPlus } = inject('cart')

const purchasedItems = ref([])

const fetchData = async () => {
  try {
    const { data } = await axios.get('https://a802cbe354cb10b1.mokky.dev/orders')
    const dataCopy = data.flatMap((item) => item.items)
    purchasedItems.value = dataCopy.map((item) => ({ ...item, isAdded: false }))
  } catch (err) {
    console.error(err)
  }
}
const onAddToFavourite = async (purchasedItems) => {
  await addToFavourite(purchasedItems)
}
onMounted(async () => {
  const localCart = localStorage.getItem('cart')
  cart.value = localCart ? JSON.parse(localCart) : []

  await fetchData()
  await fetchFavourites()

  purchasedItems.value = purchasedItems.value.map((item) => ({
    ...item,
    isAdded: cart.value.some((cartItem) => cartItem.id === item.id)
  }))
})

watch(cart, () => {
  purchasedItems.value = purchasedItems.value.map((item) => ({
    ...item,
    isAdded: false
  }))
})
console.log(purchasedItems)
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Мои покупки</h2>
  <div class="mt-10" v-auto-animate>
    <CardList
      :items="purchasedItems"
      @addToFavourite="onAddToFavourite"
      @addToCart="onClickAddPlus"
    />
    <InfoBlock
      v-if="purchasedItems.length === 0"
      title="У вас нет заказов"
      :description="`Вы нищеброд? Оформите хотя бы один заказ`"
      image-url="/emoji-2.png"
    />
  </div>
</template>
