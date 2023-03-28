<template>
    <div class="page-login">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title has-text-danger">Login</h1>

                <form @submit.prevent="submitForm">
                    <div class="field">
                        <label for="username">Username</label>
                        <div class="control">
                            <input v-model="username" type="text" name="username" id="username" class="input">
                        </div>
                    </div>

                     <div class="field">
                        <label for="password">Password</label>
                        <div class="control">
                            <input v-model="password" type="password" name="password" id="password" class="input">
                        </div>
                    </div>

                    <div class="notification is-danger" v-if="errors.length">
                        <p v-for="(error, index) in errors" :key="index">{{ error }}</p>
                    </div>

                    <div class="field">
                        <div class="control">
                            <button class="button is-danger">Login</button>
                        </div>
                    </div>

                    <hr>
                    Or <router-link to="/sign-up" class="has-text-danger">click here</router-link> to sign up!
                </form>
            </div>
        </div>
    </div>
</template>
<script>
import axios from 'axios'
import { toast } from 'bulma-toast'

export default {
    name: 'LogIn',

    data(){
        return{
            username: '',
            password: '',
            errors: [],
        }
    },

    methods:{
        async submitForm(){
            axios.defaults.headers.common['Authorization'] = ''

            localStorage.removeItem('token')

            const formData = {
                username: this.username,
                password: this.password
            }

            await axios.post('/api/v1/token/login/', formData).then(response=>{
                const token = response.data.auth_token

                this.$store.commit('setToken', token)

                axios.defaults.headers.common['Authorization'] = 'Token ' + token

                localStorage.setItem('token', token)

                toast({
                    message: 'Login successfully !',
                    type: 'is-success',
                    dismissible: true,
                    pauseOnHover: true,
                    duration: 2000,
                    position: 'bottom-right'
                })

                const toPath = this.$route.query.to || '/cart'

                this.$router.push(toPath)
            }).catch(err =>{
                if(err.response){
                        for(const property in err.response.data){
                            this.errors.push(`${property}: ${err.response.data[property]}`)
                        }

                        console.log(JSON.stringify(err.response.data))
                    }else if(err.message){
                        this.errors.push('Something went wrong. Please try again')

                        console.log(JSON.stringify(err));
                    }
            })
        }
    },

    mounted(){
        document.title = 'Login | Shop'
    }
}
</script>