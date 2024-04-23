<script setup>
import Card from './Card.vue'

defineProps({
  items: Array
})

const emit = defineEmits(['addToFavourite', 'addToCart'])
</script>
<template>
  <div class="grid grid-cols-4 gap-5" v-auto-animate>
    <Card
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :title="item.title"
      :imageUrl="item.imageUrl"
      :price="item.price"
      :onClickFavourite="() => emit('addToFavourite', item)"
      :isFavourite="item.isFavourite"
      :onClickAdd="() => emit('addToCart', item)"
      :isActive="item.isActive"
      :isAdded="item.isAdded"
    />
  </div>
</template>
