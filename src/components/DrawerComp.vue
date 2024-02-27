<script setup>
    import DrawerHeadComp from './DrawerHeadComp.vue';
    import CartItemList from './CartItemList.vue';
</script>

<template>
    <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70">
        <div class="bg-white w-96 h-full fixed right-0 tip-0 z-20 p-8">
            <DrawerHeadComp />

            <CartItemList />
            
            <div class="flex flex-col gap-4 mt-7">
                <div class="flex gap-2">
                    <span>Того:</span>
                    <div class="flex-1 border-b border-dashed"></div>
                    <b>12900</b>
                </div>

                <div class="flex gap-2">
                    <span>Налог 5%:</span>
                    <div class="flex-1 border-b border-dashed"></div>
                    <b>900</b>
                </div>
            </div>

            <button class="mt-4 disabled:bg-slate-300 transition bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700">
                Оформит заказ
            </button>
        </div>
    </div>
</template>