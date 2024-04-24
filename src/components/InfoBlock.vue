<script setup>
defineProps({
  title: String,
  description: String,
  imageUrl: String
})
</script>
<template>
  <div class="flex flex-col items-center text-center w-72 mx-auto">
    <div class="flex flex-col items-center">
      <img height="70" width="70" :src="imageUrl" alt="Info image" />
      <h2 class="mt-4 text-2xl font-medium">{{ title }}</h2>
      <p class="text-gray-400 mt-2">{{ description }}</p>
    </div>
    <RouterLink
      class="flex items-center justify-center font-semibold gap-4 mt-12 bg-lime-500 w-4/5 rounded-2xl py-3 text-white cursor-pointer"
      to="/"
    >
      <svg
        class="cursor-pointer rotate-180"
        width="14"
        height="14"
        viewBox="0 0 16 16"
        fill="none"
        xmlns="http://www.w3.org/2000/svg"
      >
        <path
          d="M1 7H14.7143"
          stroke="white"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        />
        <path
          d="M8.71436 1L14.7144 7L8.71436 13"
          stroke="white"
          stroke-width="2"
          stroke-linecap="round"
          stroke-linejoin="round"
        />
      </svg>
      Вернуться назад
    </RouterLink>
  </div>
</template>
