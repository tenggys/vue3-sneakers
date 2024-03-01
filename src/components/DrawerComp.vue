<script setup>
    import { ref, computed, inject} from 'vue';
    import axios from 'axios';

    import DrawerHeadComp from './DrawerHeadComp.vue';
    import CartItemList from './CartItemList.vue';
    import InfoBlockComp from './InfoBlockComp.vue';

    const props = defineProps({
        totalPrice: Number,
        vatPrice: Number
    })

    const { cart } = inject('cart');
    

    const isCreating = ref(false);
    const orderId = ref(null);

    const creatOrder = async () => {
        try {
            isCreating.value = true;
        
        const {data} = await axios.post(`https://d9d60d18be01acdb.mokky.dev/orders`, {
            items: cart.value, 
            totalPrice: props.totalPrice.value
        });

        cart.value = [];

        orderId.value = data.id
        } catch (error) {
            console.log(error);
        } finally {
            isCreating.value = false;
        }
    }

    const cartIsEpty = computed(() => cart.value.length === 0);
    const battonDisabled = computed(() => isCreating.value || cartIsEpty.value);

</script>

<template>
    <div class="fixed top-0 left-0 h-full w-full bg-black z-10 opacity-70">
        <div class="bg-white w-96 h-full fixed right-0 top-0 z-20 p-8">
            <DrawerHeadComp />

            <div v-if="!totalPrice || orderId" class="flex h-full items-center">
                <InfoBlockComp 
                    v-if="!totalPrice && !orderId"
                    title="Корзина пуста"
                    description="Добавьте хотя бы одну пару кроссовок, чтобы сделать заказ"
                    image-url="/package-icon.png"
                />

                <InfoBlockComp 
                    v-if="orderId"
                    title="Заказ оформлен!"
                    :description="`Ваш заказ #${orderId} скоро будет передан курьерской доставке`"
                    image-url="/order-success-icon.png"
                />
            </div>


            <div v-else>
                <CartItemList />
            
                <div class="flex flex-col gap-4 mt-7">
                    <div class="flex gap-2">
                        <span>Того:</span>
                        <div class="flex-1 border-b border-dashed"></div>
                        <b>{{ totalPrice }} руб.</b>
                    </div>

                    <div class="flex gap-2">
                        <span>Налог 5%:</span>
                        <div class="flex-1 border-b border-dashed"></div>
                        <b>{{ vatPrice }} руб.</b>
                    </div>
                </div>

                <button 
                    :disabled="battonDisabled"
                    @click="creatOrder"
                    class="mt-4 disabled:bg-slate-300 transition bg-lime-500 w-full rounded-xl py-3 text-white hover:bg-lime-600 active:bg-lime-700">
                    Оформит заказ
                </button>
            </div>
        </div>
    </div>
</template>