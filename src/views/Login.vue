<template>
    <div class="page-log-in">
        <div class="columns">
            <div class="column is-4 is-offset-4">
                <h1 class="title">Log in</h1>

                <form @submit.prevent="submitForm">
                    <div class="field">
                        <label>Username</label>
                        <div class="control">
                            <input type="text" class="input" v-model="username">
                        </div>
                    </div>

                    <div class="field">
                        <label>Password</label>
                        <div class="control">
                            <input type="password" class="input" v-model="password" id="pass">
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
                            <button class="button is-dark">Log in</button>
                        </div>
                    </div>

                    <hr>

                    Or <router-link to="/signup">click here</router-link> to sign up!
                </form>
            </div>
        </div>
    </div>
</template>

<script>
import axios from 'axios';

export default{
    name:'Login',
    data(){
        return {
            username:'',
            password:'',
            errors:[],
        }
    },
    mounted(){
        document.title = 'Login | Djackets';
    },
    methods:{
        showPassword(){
            let pass = document.getElementById('pass');
            if (pass.type === 'password'){
                pass.type = 'text';
            }else{
                pass.type = 'password';
            }
        },
        async submitForm(){
            axios.defaults.headers.common['Authorization'] = '';
            localStorage.removeItem('token');

            const formData = {
                username:this.username,
                password:this.password,
            };

            await axios
                .post("/api/v1/token/login/", formData)
                .then(response => {
                    const token = response.data.auth_token
                    this.$store.commit('setToken', token)
                    
                    axios.defaults.headers.common["Authorization"] = "Token " + token
                    localStorage.setItem("token", token)
                    const toPath = this.$route.query.to || '/cart'
                    this.$router.push(toPath)
                })
                .catch(error => {
                    if (error.response) {
                        for (const property in error.response.data) {
                            this.errors.push(`${property}: ${error.response.data[property]}`)
                        }
                    } else {
                        this.errors.push('Something went wrong. Please try again')
                        
                        console.log(JSON.stringify(error))
                    }
                })
        }
    }
}
</script>

<style scoped>
.page-log-in{
    padding:20px;
    box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
}
</style>