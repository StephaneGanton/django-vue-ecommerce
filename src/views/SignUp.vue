<template>
    <div class="sign-up-page">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title has-text-danger">Sign Up</h1>

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

                    <div class="field">
                        <label for="password2">Repeat Password</label>
                        <div class="control">
                            <input v-model="password2" type="password" name="password2" id="password2" class="input">
                        </div>
                    </div>

                    <div class="notification is-danger" v-if="errors.length">
                        <p v-for="(error, index) in errors" :key="index">{{ error }}</p>
                    </div>

                    <div class="field">
                        <div class="control">
                            <button class="button is-danger">Sign up</button>
                        </div>
                    </div>

                    <hr>
                    Or <router-link to="/log-in" class="has-text-danger">click here</router-link> to log in!
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios'
import { toast } from 'bulma-toast'

export default {
    name: 'SignUp',
    data(){
        return{
            username: '',
            password: '',
            password2: '',
            errors: [],
        }
    },

    methods:{
        submitForm(){
            this.errors = []

            if(this.username === ''){
                this.errors.push('The username is missing')
            }
            if(this.password === ''){
                this.errors.push('The password is too short')
            }

            if(this.password !==  this.password2){
                this.errors.push('The passwords don\'t match')
            }

            if(!this.errors.length){
                const formData = {
                    username: this.username,
                    password: this.password
                }

                axios.post('/api/v1/users/', formData).then(response=>{

                    toast({
                        message: 'Account created, please log in!',
                        type: 'is-success',
                        dismissible: true,
                        pauseOnHover: true,
                        duration: 2000,
                        position: 'bottom-right'
                    })

                    this.$router.push('/log-in')
                }).catch(e=>{
                    if(e.response){
                        for(const property in e.response.data){
                            this.errors.push(`${property}: ${e.response.data[property]}`)
                        }

                        console.log(JSON.stringify(e.response.data))
                    }else if(e.message){
                        this.errors.push('Something went wrong. Please try again')

                        console.log(JSON.stringify(e));
                    }
                })
            }
        }
    }
}
</script>