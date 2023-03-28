<template>
    <div class="page-search">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h1 class="title has-text-danger">Search</h1>
                <h2 class="is-size-5 has-tex-grey">Search term: "{{ query }}"</h2>
            </div>
            <div v-if="!produits.length" class="column is-12 has-text-centered">No products associated.</div>
            <Product-box v-else v-for="(product, index) in produits" :key="index" :product="product"/>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { toast } from 'bulma-toast'
import ProductBox from '@/components/ProductBox.vue'

export default {
    name: 'Search',

    components:{
        ProductBox,
    },
    data(){
        return{
            produits:[],
            query: '',
        }
    },

    methods:{
        async performSearch(){
            this.$store.commit('setIsLoading', true)

            await axios.post('api/v1/products/search/', {'query': this.query}).then(resp=>{
                this.produits = resp.data
            }).catch(e=>{
                console.log(e);
            })

            this.$store.commit('setIsLoading', false)
        }
    },

    mounted(){
        document.title ='search | Shop'

        let uri = window.location.search.substring(1)
        let params = new URLSearchParams(uri)

        if(params.get('query')){
            this.query = params.get('query')
            
            this.performSearch()
        }
    }
}
</script>