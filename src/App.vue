<script setup>
import { onMounted, provide, reactive, ref, watch } from 'vue'
import axios from 'axios'

import Header from './components/Header.vue'
import CardList from './components/CardList.vue'

const items = ref([])

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})

const onChangeSelect = (event) => {
  filters.sortBy = event.target.value
}

const onChangeSearchInput = (event) => {
  filters.searchQuery = event.target.value
}

const fetchFavourites = async () => {
  try {
    const { data: favourites } = await axios.get(`https://a802cbe354cb10b1.mokky.dev/favourites`)
    items.value = items.value.map((item) => {
      const favourite = favourites.find((favourite) => favourite.parentId === item.id)

      if (!favourite) {
        return item
      }

      return {
        ...item,
        isFavourite: true,
        favouriteId: favourite.id
      }
    })
  } catch (err) {
    console.log(err)
  }
}

const addToFavourite = async (item) => {
  try {
    if (!item.isActive) return
    if (!item.isFavourite) {
      const obj = {
        parentId: item.id
      }
      item.isFavourite = true
      if (item.isActive) {
        item.isActive = false

        const data = await axios.post(`https://a802cbe354cb10b1.mokky.dev/favourites`, obj)
        item.favouriteId = data.data.id
        if (data.status === 201) {
          item.isActive = true
        }
      }
    } else if (item.isActive) {
      item.isActive = false
      item.isFavourite = false
      const data = await axios.delete(
        `https://a802cbe354cb10b1.mokky.dev/favourites/${item.favouriteId}`
      )

      item.favouriteId = null
      if (data.status === 200) item.isActive = true
    }
  } catch (err) {
    console.log(err)
  }
}

const fetchItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy
    }

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }
    const { data } = await axios.get(`https://a802cbe354cb10b1.mokky.dev/items`, {
      params
    })

    items.value = data.map((obj) => ({
      ...obj,
      isFavourite: false,
      isActive: true,
      favouriteId: null,
      isAdded: false
    }))
  } catch (err) {
    console.log(err)
  }
}

onMounted(async () => {
  await fetchItems()
  await fetchFavourites()
})

watch(filters, fetchItems)

provide('addToFavourite', addToFavourite)
</script>

<template>
  <!-- <Drawer /> -->
  <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
    <Header />

    <div class="p-10">
      <div class="flex justify-between items-center">
        <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>
        <div class="flex gap-4">
          <select @change="onChangeSelect" class="py-2 px-3 border rounded-md outline-none">
            <option value="name">По названию</option>
            <option value="price">По цене (дешевые)</option>
            <option value="-price">По цене (дорогие)</option>
          </select>
          <div class="relative">
            <img class="absolute left-4 top-3" src="/search.svg" />
            <input
              @input="onChangeSearchInput"
              class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400"
              type="text"
              placeholder="Поиск..."
            />
          </div>
        </div>
      </div>
      <div class="mt-10">
        <CardList :items="items" @addToFavourite="addToFavourite" />
      </div>
    </div>
  </div>
</template>
