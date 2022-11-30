<template>
    <div class="page-sign-up">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">Sign up</h1>

                <form @submit.prevent="submitForm">
                    
                    <label for="username">Username</label>
                    <div class="control">
                        <input type="text" class="input" v-model="username" />
                    </div>

                    <div class="field">
                        <label for="password">Password</label>
                        <div class="control">
                            <input type="password" class="input" v-model="password" id='pass1'/>
                        </div>
                    </div>

                    <div class="field">
                        <label for="password">Password</label>
                        <div class="control">
                            <input type="password" class="input" v-model="password2" id='pass2' />
                        </div>
                    </div>

                    <div class="notification is-danger" v-if="errors.length">
                        <p v-for="error in errors" v-bind:key="error">{{ error }}</p>
                    </div>

                    <div class="field">
                        <div class="control">
                            <label class="checkbox">
                                <input type="checkbox" @click="showPassword">
                                Show Password
                            </label>
                        </div>
                    </div>

                    <div class="field">
                        <div class="control">
                            <button class="button is-dark">Sign Up</button>
                        </div>
                    </div><hr />

                    Or <router-link to="/login">Click here</router-link> to log in!
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';
import { toast } from 'bulma-toast';

export default{
    name:'SignUp',
    data(){
        return{
            username:'',
            password:'',
            password2:'',
            errors:[],
        }
    },mounted(){
        document.title = 'Sign up | Djackets';
    },
    methods:{
        submitForm(){
            this.errors = [];

            if(this.username === ''){
                this.errors.push('The username is missing!');
            };

            if(this.password === ''){
                this.errors.push('The password is too short!');
            };

            if(this.password !== this.password2){
                this.errors.push('The passwords don\'t match!');
            };

            if(!this.errors.length){
                const formData = {
                    username:this.username,
                    password:this.password
                }

                axios
                    .post('/api/v1/users/',formData)
                    .then(res=>{
                        toast({
                            message: 'Account created, please log in!',
                            type: 'is-success',
                            dismissible: true,
                            pauseOnHover: true,
                            duration: 2000,
                            position: 'bottom-right',
                        })
                        this.$router.push('/login')
                    })
                    .catch(error => {
                        if (error.response) {
                            for (const property in error.response.data) {
                                this.errors.push(`${property}: ${error.response.data[property]}`)
                            }
                            console.log(JSON.stringify(error.response.data))
                        } else if (error.message) {
                            this.errors.push('Something went wrong. Please try again')
                            
                            console.log(JSON.stringify(error))
                        }
                        console.log(error.response.data)
                    })
            }

        },
        showPassword(){
            let pass1 = document.getElementById('pass1');
            let pass2 = document.getElementById('pass2');
            if (pass1.type === 'password' || pass2.type === 'password'){
                pass1.type = 'text';
                pass2.type = 'text';
            }else{
                pass1.type = 'password';
                pass2.type = 'password';
            }
        },
    }
}
</script>

<style scoped>
.page-sign-up{
    padding:20px;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
}
</style>