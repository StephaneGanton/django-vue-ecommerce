<template>
    <div class="page-category">
        <div class="columns is-multiline">
            <div class="column is-12">
                <h2 class="is-size-2 has-text-centered has-text-danger">{{ category.name }}</h2>
            </div>

            <div v-if="!category.produits.length" class="column is-12 has-text-centered">No products associated.</div>
            <Product-box v-else v-for="(product, index) in category.produits" :key="index" :product="product"/>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { toast } from 'bulma-toast'
import ProductBox from '@/components/ProductBox.vue'

export default {
    name: 'Category',
    components:{
        ProductBox,
    },
    data(){
        return{
            category:{
                produits: []
            }
        }
    },

    methods:{
        async getCategory(){
            const categorySlug = this.$route.params.category_slug

            this.$store.commit('setIsLoading', true)

            await axios.get(`api/v1/products/${categorySlug}`).then(resp=>{
                this.category = resp.data

                document.title = this.category.name + ' | Shop'
            }).catch(e=>{
                    console.log(e);

                    toast({
                    message: 'Something went wrong. Please try again.',
                    type: 'is-danger',
                    dismissible: true,
                    pauseOnHover: true,
                    duration: 3000,
                    position: 'bottom-right'
                })
            })

            this.$store.commit('setIsLoading', false)
        }
    },

    mounted(){
        this.getCategory()
    },

    watch:{
        $route(to, from){
            if(to.name == 'Category'){
                this.getCategory()
            }
        }
    }
}
</script>