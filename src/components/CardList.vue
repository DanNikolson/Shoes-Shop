<script setup>
import Card from './Card.vue'

defineProps({
  items: Array,
  isFavourites: Boolean
})

const emit = defineEmits(['addToFavourite', 'addToCart'])
</script>
<template>
  <div
    class="grid grid-cols-4 gap-5 max-[768px]:grid-cols-1 max-[1100px]:grid-cols-2"
    v-auto-animate
  >
    <Card
      v-for="item in items"
      :key="item.id"
      :id="item.id"
      :title="item.title"
      :imageUrl="item.imageUrl"
      :price="item.price"
      :onClickFavourite="isFavourites ? null : () => emit('addToFavourite', item)"
      :isFavourite="item.isFavourite"
      :onClickAdd="isFavourites ? null : () => emit('addToCart', item)"
      :isActive="item.isActive"
      :isAdded="item.isAdded"
    />
  </div>
</template>
