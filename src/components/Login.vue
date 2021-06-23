<template>
  <div class="main">
    <h2 class="heading text-center text-primary" style="padding: 1em 2em;">
        Health Monitoring System <br> using Internet of Things (IoT)
    </h2>
   
    <div class="container">
        <div class="form-div">
            <h3 class="text-center">Login</h3>
            <h5 class="text-center" style="color: #FF1212;">{{ errorMessage }}</h5>
            <form @submit.prevent="login">
                <label for="username">Email</label><br>
                <input type="email" name="email" v-model="email" id="email"><br>
                <label for="password">Password</label><br>
                <input type="password" name="password" v-model="password" id="password"><br>
                <input v-show="!isProcessing" type="submit" class="btn-primary" value="Submit"><br>
                <div v-show="isProcessing" class="text-center">
                    <img v-show="true" src="/images/loading.gif" alt="Loading">
                </div>
            </form>
        </div>
    </div>
  </div>
</template>

<script>
    import axios from 'axios';

    export default {
        name: 'Login',
        
        data(){
            return {
                email: '',
                password: '',
                errorMessage: '',
                isProcessing: false
            }
        },

        methods: {
            login: function(){
                this.isProcessing = true;

                let username = this.email.trim();
                let password = this.password.trim();

                if((username !== '') && (password !== '')){

                    const headers = {
                        "Content-Type": "application/json"
                    };

                    let credentials = {
                        username,
                        password
                    };

                    axios.post('https://iot-health-monitoring-backend.herokuapp.com/api/v1/login', credentials, {headers: headers})
                        .then(response => {
                            let token = response.data.token;

                            if(token != null){
                                localStorage.setItem('token', token);
                                localStorage.setItem('IsHealthAdminLoggedIn', 'true');
                                window.location.href = '/home';
                            }else{
                                this.errorMessage = "Unauthorized access denied";
                            }
                        })
                        .catch(e => {
                            alert(e);
                        })
                }else{
                    this.errorMessage = "Provide login details";
                }

                this.isProcessing = false;
            }
        },

        computed: {
            
        }
    }
</script>

<style scoped>
    *{
        box-sizing: border-box;
    }

    .text-center{
        text-align: center;
    }

    .container{
        display: flex;
        justify-content: center;
        align-items: center;
    }

    .form-div{
        margin-top: 4%;
        background-color: #FFFFFF;
        color: #000000 !important;
        border: 1px solid #CCCCCC;
        padding: 0 1.5em 1.5em 1.5em;
        border-radius: 10px;
        box-shadow: 0 4px 8px 0 rgba(0, 0, 0, 0.2), 0 6px 20px 0 rgba(0, 0, 0, 0.19);
    }

    input{
        width: 100%;
        height: 40px;
        padding: 10px;
        margin: 10px 0px;
    }

    .text-primary{
        color: #1f1b41;
    }

    .btn-primary{
        background-color: rgb(30, 50, 73);
        color: #FFFFFF;
        border: none;
    }

    @media screen and (min-width: 760px){
        .form-div{
            width: 30% !important;
        }
    }

    @media screen and (max-width: 450px){
        .heading{
            font-size: 1rem;
        }
        .form-div{
            width: 90% !important;
        }
    }
</style>
