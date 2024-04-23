<script setup>
import { ref, onMounted } from 'vue'
import axios from 'axios'

const favourites = ref([])
onMounted(async () => {
  try {
    const data = await axios.get('https://a802cbe354cb10b1.mokky.dev/favourites')
    console.log(data)
  } catch (err) {
    console.log(err)
  }
})
</script>

<template>
  <h1>Мои закладки</h1>
</template>
