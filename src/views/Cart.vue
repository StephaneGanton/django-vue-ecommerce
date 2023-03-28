<template>
    <div class="page-category">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h2 class="is-size-2 has-text-centered has-text-danger">Cart</h2>
            </div>

            <div class="column is-12 box">
                <table class="table is-fullwidth" v-if="cartTotalLength">
                    <thead>
                        <tr>
                            <th>Product</th>
                            <th class="has-text-centered">Price</th>
                            <th class="has-text-centered">Quantity</th>
                            <th class="has-text-centered">Total</th>
                            <th class="has-text-centered"></th>
                        </tr>
                    </thead>
                    <tbody>
                        <CartItem 
                            v-for="(item, index) in cart.items" 
                            :key="index" 
                            :initialItem="item" 
                            @removeFromCart="removeFromCart"/>
                    </tbody>
                </table>
                <p v-else class="is-size-10 has-text-centered">You don't have any products in your cart...</p>
            </div>

            <div class="column is-12 box" v-if="cartTotalLength">
                <h2 class="subtitle">Summary</h2>
                <strong>${{ cartTotalPrice.toFixed(2) }}</strong>. There is <span class="has-text-grey">{{ cartTotalLength }}</span> items in your cart
                <hr>

                <router-link to="/cart/checkout" class="button is-dark">Proceed Checkout</router-link>
            </div>
        </div>
    </div>
</template>
<script>
import axios from 'axios'
import CartItem from '@/components/CartItem.vue'

export default {
    name: 'Cart',
    components:{
        CartItem,
    },

    computed:{
        cartTotalLength(){
            return this.cart.items.reduce((acc, curVal)=>{
                return acc += curVal.quantity
            }, 0)
        },

        cartTotalPrice(){
            return this.cart.items.reduce((acc, curVal)=>{
                return acc += curVal.product.price * curVal.quantity
            }, 0)
        }
    },

    data(){
        return{
            cart:{
                items: []
            }
        }
    },

    methods:{
        removeFromCart(item){
            this.cart.items = this.cart.items.filter(i => i.product.id !==  item.product.id)
        }
    },

    mounted(){
        this.cart = this.$store.state.cart
    }
}
</script>