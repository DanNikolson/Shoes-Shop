<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'
import CardList from '@/components/CardList.vue'
import InfoBlock from '@/components/InfoBlock.vue'

const purchasedItems = ref([])
const showInfo = ref(false)

const fetchData = async () => {
  try {
    const { data } = await axios.get('https://a802cbe354cb10b1.mokky.dev/orders')
    const dataCopy = data.flatMap((item) => item.items)
    purchasedItems.value = dataCopy.map((item) => ({ ...item, isAdded: false }))
  } catch (err) {
    console.error(err)
  }
}
onMounted(async () => {
  await fetchData()
  setTimeout(() => {
    showInfo.value = true
  }, 500)
})
</script>

<template>
  <h2 class="text-3xl font-bold mb-8">Мои покупки</h2>
  <div class="mt-10" v-auto-animate>
    <CardList :items="purchasedItems" isFavourites />
    <InfoBlock
      v-if="purchasedItems.length === 0 && showInfo"
      title="У вас нет заказов"
      :description="`Вы нищеброд? Оформите хотя бы один заказ`"
      image-url="/emoji-2.png"
    />
  </div>
</template>
