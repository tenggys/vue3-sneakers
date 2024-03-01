<script setup>
  const props = defineProps({
    id: Number,
    imageUrl: String,
    title: String,
    price: Number,
    isFavorite: Boolean,
    isAdded: Boolean,
    onClickFavorite: Function,
    onClickAdd: Function
  })

  const visibleFavoriteButton = Boolean(props.onClickAdd)

</script>

<template>
    <div class="relative bg-white borger border-slate-100 rounded-3xl p-8 cursor-pointer transition hover:-translate-y-2 hover:shadow-xl">
        <img 
          v-if="visibleFavoriteButton"
          :src="!isFavorite ? '/like-1.svg' : 'like-2.svg'" 
          alt="Like 1" 
          class="absolute top-0 left-0"
          @click="onClickFavorite"
        >

        <img :src="imageUrl" alt="Sneaker">

        <p class="mt-2">{{ title }}</p>

        <div class="flex justify-between mt-5">
          <div class="flex flex-col">
            <span class="text-slate-400">Цена:</span>
            <b>{{ price }}</b>
          </div>

          <img v-if="onClickFavorite" @click="onClickAdd" :src="!isAdded ? '/plus.svg' : 'checked.svg'" alt="plus">
        </div>
    </div>
</template>