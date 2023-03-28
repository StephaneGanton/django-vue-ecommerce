<template>
    <div class="page-my-account">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title">My account</h1>
            </div>

            <div class="column is-12">
                <button @click="logout()" class="button is-danger">Log out</button>
            </div>

            <hr>

            <div class="column is-12">
                <h2 class="subtitle">My Orders</h2>

                <OrderSummary 
                    v-for="(order, index) in orders"
                    :key="index"
                    :order="order"/>
            </div>

        </div>
    </div>
</template>

<script>
import axios from 'axios'
import OrderSummary from '@/components/OrderSummary.vue'

export default {
    name: 'MyAccount',
    components:{
        OrderSummary,
    },

    data(){
        return{
            orders: []
        }
    },

    methods:{
        logout(){
            axios.defaults.headers.common['Authorization'] = ''
            localStorage.removeItem('token')
            localStorage.removeItem('username')
            localStorage.removeItem('userid')

            this.$store.commit('removeToken')
            this.$router.push('/')
        },

        async getMyOrders(){
            this.$store.commit('setIsLoading', True)

            await axios.get('/api/v1/orders/').then(resp=>{
                this.orders = resp.data
            }).catch(err =>{
                console.log(err);
            })

            this.$store.commit('setIsLoading', false)
        }
    },

      mounted(){
        document.title = 'My account | Shop'
        this.getMyOrders()
    },
}
</script>