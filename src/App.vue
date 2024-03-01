<script setup>
  import { ref, provide, computed } from 'vue';

  import DrawerComp from './components/DrawerComp.vue';
  import HeaderComp from './components/HeaderComp.vue';

  /* Корзина (START)*/
  const cart = ref([]);


  const drawerOpen = ref(false);

  const totalPrice = computed(() => cart.value.reduce((acc, item) => acc + item.price, 0));
  const vatPrice = computed(() => Math.round((totalPrice.value * 5) / 100));

  const closeDrawer = () => {
    drawerOpen.value = false
  };

  const openDrawer = () => {
    drawerOpen.value = true
  };



  const addToCart = (item) => {
    cart.value.push(item);
    item.isAdded = true;

  }

  const removeFromCart = (item) => {
    cart.value.splice(cart.value.indexOf(item), 1)
    item.isAdded = false;
  }

  provide('cart', {
    cart,
    closeDrawer,
    openDrawer,
    addToCart,
    removeFromCart
  });
  /* Корзина (END)*/

</script>

<template>
  <div>
    <DrawerComp 
      v-if="drawerOpen" 
      :total-price="totalPrice" 
      :vat-price="vatPrice" 
    />
    
    <div class="bg-white w-4/5 m-auto rounded-xl shadow-xl mt-14">
      <HeaderComp :total-price="totalPrice" @open-drawer="openDrawer"/>

      <div class="p-10">
        <router-view></router-view>
      </div>
    </div>
  </div>
  
</template>

<style scoped>

</style>
