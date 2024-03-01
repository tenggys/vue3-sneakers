<script setup>
import { onMounted, ref, reactive, watch, provide, computed } from 'vue';
import axios from 'axios'

import CardList from './components/CardList.vue';
import DrawerComp from './components/DrawerComp.vue';
import HeaderComp from './components/HeaderComp.vue';

const items = ref([]);
const cart = ref([]);
const isCreatingOrder = ref(false);

const drawerOpen = ref(false);

const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0));

const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100));

const cartIsEpty = computed(() => cart.value.length === 0);
const cartBattonDisabled = computed(() => isCreatingOrder.value || cartIsEpty.value);

const closeDrawer = () => {
  drawerOpen.value = false
};

const openDrawer = () => {
  drawerOpen.value = true
};

const filters = reactive({
  sortBy: 'title',
  searchQuery: ''
});

const addToCart = (item) => {
  cart.value.push(item);
  item.isAdded = true;

}

const removeFromCart = (item) => {
  cart.value.splice(cart.value.indexOf(item), 1)
  item.isAdded = false;
}

const creatOrder = async () => {
  try {
    isCreatingOrder.value = true;
    const {data} = await axios.post(`https://d9d60d18be01acdb.mokky.dev/orders`, {
      items: cart.value, 
      totalPrice: totalPrice.value
    });

    cart.value = [];

    return data;
  } catch (error) {
    console.log(error);
  } finally {
    isCreatingOrder.value = false;
  }
}

const onClickAddPlus = (item) => {
  if (!item.isAdded) {
    addToCart(item);
  } else {
    removeFromCart(item)
  }
}


const onChangeSelect = event => {
  filters.sortBy = event.target.value;
};

const onchangeSearchInput = event => {
  filters.searchQuery = event.target.value;
};

const fetchFavorites = async () => {
  try {

    const {data: favorites} = await axios.get(`https://d9d60d18be01acdb.mokky.dev/favorites`)

    items.value = items.value.map((item) => {
      const favorite = favorites.find((favorite) => favorite.parentID === item.id);

      if (!favorite) {
        return item;
      }
 
      return {
        ...item,
        isFavorite: true,
        favoriteId: favorite.id,
      }
    });

  } catch (error) {
    console.log(error);
  }
};

const addToFavorite = async (item) => {
  try {
    if (!item.isFavorite) {
      const obj = {
        parentId: item.id
      }

      item.isFavorite = true;

      const {data} = await axios.post(`https://d9d60d18be01acdb.mokky.dev/favorites`, obj);

      item.favoriteId = data.id;
      } else {
        item.isFavorite = false;
        await axios.delete(`https://d9d60d18be01acdb.mokky.dev/favorites/${item.favoriteId}`);
        item.favoriteId = null;
      }
  } catch (error) {
    console.log(error);
  }

}

const feactItems = async () => {
  try {
    const params = {
      sortBy: filters.sortBy,
    };

    if (filters.searchQuery) {
      params.title = `*${filters.searchQuery}*`
    }

    const {data} = await axios.get(`https://d9d60d18be01acdb.mokky.dev/items`, { params })

    items.value = data.map(obj => ({
      ...obj,
      isFavorite: false,
      favoriteId: null,
      isAdded: false
    }));

  } catch (error) {
    console.log(error);
  }
}

onMounted(async () => {
  await feactItems();
  await fetchFavorites();
});

watch(filters, feactItems);

watch(cart, () => {
  items.value = items.value.map((item) => ({
    ...item,
    isAdded: false
  }))
});

provide('cart', {
  cart,
  closeDrawer,
  openDrawer,
  addToCart,
  removeFromCart
});



</script>

<template>
  <div>
    <DrawerComp 
      v-if="drawerOpen" 
      :total-price="totalPrice" 
      :vat-price="vatPrice" 
      @creat-order="creatOrder"
      :batton-disabled="cartBattonDisabled"
    />
    
    <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
      <HeaderComp :total-price="totalPrice" @open-drawer="openDrawer"/>

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
          <CardList :items="items" @add-to-favorite="addToFavorite" @add-to-cart="onClickAddPlus"/>
        </div>
      </div>
    </div>
  </div>
  
</template>

<style scoped>

</style>
