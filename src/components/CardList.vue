<script setup>
    import CardComp from './CardComp.vue';

    const onClickAdd = () => {
        alert('Товар добавлен')
    }
</script>

<template>
    <div class="grid grid-cols-4 gap-5">
        <CardComp title="Мужские кросовки Nike Blaze Mid Suede" 
            imageUrl="/sneakers/sneakers-1.jpg" 
            :price="15000"
            :isFavorite="false"
            :onClickAdd="onClickAdd"
        />
      </div>
</template>