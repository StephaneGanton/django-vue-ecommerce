<template>
  <div class="home">
    <section class="hero is-medium is-dark mb-6">
      <div class="hero-body has-text-centered">
        <p class="title mb-6">
          Welcome to Shopping
        </p>
        <p class="subtitle">
          The best Jacket store online
        </p>
      </div>
    </section>

    <div class="columns is-multiline">
      <div class="column is-12">
        <h2 class="is-size-2 has-text-centered has-text-danger">Latest Products</h2>
      </div>

      <div v-if="latestProducts.length == 0" class="column is-12 has-text-centered">Empty</div>

      <Product-box v-for="(product, index) in latestProducts" :key="index" :product="product"/>
    </div>
  </div>
</template>

<script>
// @ is an alias to /src
//import HelloWorld from '@/components/HelloWorld.vue'

import axios from 'axios'
import ProductBox from '@/components/ProductBox.vue'

export default {
  name: 'Home',
  components: {
    ProductBox,
  }, 
  
  data(){
    return{
      latestProducts: [],
    }
  },

  mounted(){
    this.getLatestProducts(),
    document.title = 'Home | Shop'
  },

  methods:{
    async getLatestProducts(){
      this.$store.commit('setIsLoading', true)

      await axios.get('api/v1/latest-products/').then(response=>{
        this.latestProducts = response.data

      }).catch(e=>{
        console.log(e);
      })

      this.$store.commit('setIsLoading', false)

    }
  }
}
</script>


