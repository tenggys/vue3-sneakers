<script setup>
import { onMounted, ref, reactive, watch } from 'vue';
import axios from 'axios'

import CardList from './components/CardList.vue';
//import DrawerComp from './components/DrawerComp.vue';
import HeaderComp from './components/HeaderComp.vue';

const items = ref([]);

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
})


const onChangeSelect = event => {
  filters.sortBy = event.target.value;
};

const onchangeSearchInput = event => {
  filters.searchQuery = event.target.value;
};

const feactItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy,
      //searchQuery: filters.searchQuery
    };

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const {data} = await axios.get(`https://d9d60d18be01acdb.mokky.dev/items`, { params })

    items.value = data;

  } catch (error) {
    console.log(error);
  }
}

onMounted(feactItems);

watch(filters, feactItems)



</script>

<template>
  <div>
    <!--<DrawerComp /> -->
    
    <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
      <HeaderComp />

      <div class="p-10">
        <div class="flex justify-between items-center">
          <h2 class="text-3xl font-bold mb-8">Все кроссовки</h2>

          <div class="flex gap-4">
            <select @change="onChangeSelect" class="py-2 px-3 border rounded-mb outline-none">
              <option value="name">По названию</option>
              <option value="price">По цене (дешевые)</option>
              <option value="-price">По цене (дорогие)</option>
            </select>

            <div class="relative">
              <img class="absolute left-4 top-3" src="/search.svg" alt="search">
              <input @input="onchangeSearchInput" class="border rounded-md py-2 pl-11 pr-4 outline-none focus:border-gray-400" 
                type="text" 
                placeholder="Поиск...">
            </div>
          </div>

        </div>

        <div class="mt-10">
          <CardList :items="items "/>
        </div>
      </div>
    </div>
  </div>
  
</template>

<style scoped>

</style>
