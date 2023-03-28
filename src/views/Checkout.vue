<template>
    <div class="page-checkout">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title has-text-danger">Checkout</h1>
            </div>

            <div class="column is-12 box">
                <table class="table is-fullwidth">
                    <thead>
                        <tr>
                            <th>Product</th>
                            <th class="has-text-centered">Price</th>
                            <th class="has-text-centered">Quantity</th>
                            <th class="has-text-centered">Total</th>
                        </tr>
                    </thead>
                    <tbody>
                        <tr v-for="(item, index) in cart.items" :key="index">
                            <td>{{ item.product.name }}</td>
                            <td class="has-text-centered">${{ item.product.price }}</td>
                            <td class="has-text-centered">{{ item.quantity }}</td>
                            <td class="has-text-centered">${{ getItemTotal(item).toFixed(2) }}</td>
                        </tr>
                    </tbody>
                    <tfoot>
                        <tr>
                            <td colspan="2">Total</td>
                            <td class="has-text-centered">{{ cartTotalLength }}</td>
                            <td class="has-text-centered"><strong>${{ cartTotalPrice.toFixed(2) }}</strong></td>
                        </tr>
                    </tfoot>
                </table>
            </div>

            <div class="column is-12 box">
                <h2 class="subtitle">Shipping details</h2>

                <p class="has-text-grey mb-4">* All fields are required</p>

                <div class="columns is-multiline">
                    <div class="column is-6">
                        <div class="field">
                            <label for="first_name">First name*</label>
                            <div class="control">
                                <input class="input" type="text" id="first_name" v-model="first_name" />
                            </div>
                        </div>

                        <div class="field">
                            <label for="last_name">Last name*</label>
                            <div class="control">
                                <input class="input" type="text" id="last_name" v-model="last_name">
                            </div>
                        </div>

                        <div class="field">
                            <label for="email">Email*</label>
                            <div class="control">
                                <input class="input" type="email" id="email" v-model="email">
                            </div>
                        </div>

                        <div class="field">
                            <label for="phone">Phone*</label>
                            <div class="control">
                                <input class="input" id="phone" type="text"  v-model="phone">
                            </div>
                        </div>
                    </div>

                    <div class="column is-6">
                        <div class="field">
                            <label for="address">Address*</label>
                            <div class="control">
                                <input type="text" class="input" id="address" v-model="address">
                            </div>
                        </div>

                        <div class="field">
                            <label for="zipcode">Zip Code*</label>
                            <div class="control">
                                <input type="text" class="input" id="zipcode" v-model="zipcode">
                            </div>
                        </div>

                        <div class="field">
                            <label for="place">Place*</label>
                            <div class="control">
                                <input type="text" class="input" id="place" v-model="place">
                            </div>
                        </div>
                    </div>
                </div>

                <div class="notification is-danger mt-4" v-if="errors.length">
                    <p v-for="(error, index) in errors" :key="index">{{ error }}</p>
                </div>

                <hr>

                <div id="card-element" class="mb-5"></div>

                <template v-if="cartTotalLength">
                    <hr>

                    <button class="button is-dark" @click="submitForm">Pay with Stripe</button>
                </template>
            </div>
        </div>
    </div>
</template>
<script>
import axios from 'axios'

export default {
    name: 'Checkout',

    computed:{
        cartTotalPrice(){
            return this.cart.items.reduce((acc, curVal) =>{
                return acc += curVal.product.price * curVal.quantity
            }, 0)
        },

        cartTotalLength(){
            return this.cart.items.reduce((acc, curVal) =>{
                return acc += curVal.quantity
            }, 0)
        }
    },

    data(){
        return{
            cart:{
                items: [],
            },
            stripe:{},
            card:{},
            first_name: '',
            last_name: '',
            email: '',
            phone: '',
            address: '',
            zipcode: '',
            place: '',
            errors: []
        }
    },

    methods:{
        getItemTotal(item){
            return item.quantity * item.product.price
        },

        submitForm(){
            this.errors = []

            if(this.first_name ==='') this.errors.push('The first name field is missing!')

            if(this.last_name ==='') this.errors.push('The last name field is missing!')

            if(this.email ==='') this.errors.push('The email field is missing!')

            if(this.phone ==='') this.errors.push('The phone field is missing!')

            if(this.address ==='') this.errors.push('The address field is missing!')

            if(this.zipcode ==='') this.errors.push('The zipcode field is missing!')

            if(this.place ==='') this.errors.push('The place field is missing!')


            if(!this.errors.length){
                this.$store.commit('setIsLoading', true)

                this.stripe.createToken(this.card).then(result=>{
                    if(result.error){
                        this.$store.commit('setIsLoading', false)

                        this.errors.push('Something went wrong with stripe. Please try again!')

                        console.log(result.error.message);
                    }else{
                        this.stripeTokenHandler(result.token)
                    }
                })
            }
        },

        async stripeTokenHandler(token){
            const items = []

            for (let i = 0; i < this.cart.items.length; i++){
                const item = this.cart.items[i]
                const obj = {
                    product: item.product.id,
                    quantity: item.quantity,
                    price: item.product.price * item.quantity
                }

                items.push(obj)
            }

            const data = {
                'first_name': this.first_name,
                'last_name': this.last_name,
                'email': this.email,
                'address': this.address,
                'zipcode': this.zipcode,
                'place': this.place,
                'phone': this.phone,
                'items': items,
                'stripe_token': token.id,
            }

            console.log('data... ');
            console.log(data);
            await axios.post('/api/v1/checkout/', data).then(resp=>{
                this.$store.commit('clearCart')
                this.$router.push('/cart/success')
            }).catch(err=>{
                this.errors.push('Something went wrong. Please try again!')

                console.log(err);
            })

            this.$store.commit('setIsLoading', false)
        }

    },

    mounted(){
        document.title = 'Checkout | Shop'

        this.cart = this.$store.state.cart

        key = process.env.VUE_APP_STRIPE_SECRET_KEY

        if(this.cartTotalLength > 0){
            this.stripe = Stripe(key)
            const elements = this.stripe.elements();
            this.card = elements.create('card', { hidePostalCode: true })

            this.card.mount('#card-element')
        }
    }
}
</script>