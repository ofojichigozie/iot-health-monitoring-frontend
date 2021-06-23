<template>
    <div class="main">
        <div class="top">
            <div class="container">
                <div class="top-inner">
                    <span style="color: rgb(158, 35, 35); font-weight: bold;">{{ logo }}</span>
                    <button class="logout-btn" v-on:click="logout()">Logout</button>
                </div>
            </div>
        </div>

        <div class="landing">
            <h1>HEALTH MONITORING SYSTEM</h1>
            <p style="color: #FFFF00;">With internet-of-things (IoT)</p>
        </div>

        <div class="main">
            <h4 class="">Front-end application for viewing patient's health records such as tempearture, humidity and pulse rate. These data are remotely captured by the hardware-based system. You can view all patients' data or data for a particular patient with an ID.</h4>

            <div class="main-inner">
                <div class="form-div">
                    <input type="text" class="id-field" v-model="patientID" placeholder="Enter patient's ID">
                    <button class="btn-primary get-data-btn" v-on:click="getPatientHealthDataByID()">Fetch</button>
                    <div>
                        <button class="btn-primary get-all-data-btn" v-on:click="getAllPatientHealthData()">Fetch All</button>
                    </div>
                </div>
            </div>

            <div class="container">
                <div v-if="retrievedOneData == true">
                    <div>
                        <h3 style="text-align: center; margin-top: 50px;">Health records of patient with the specified ID</h3>
                    </div>

                    <div style="overflow-x: auto;">
                        <table id="table">
                            <thead>
                                <th>S/N</th>
                                <th>Patient ID</th>
                                <th>Temperature</th>
                                <th>Humidity</th>
                                <th>Pulse Rate</th>
                                <th>Capture Date</th>
                                <th>Capture Time</th>
                            </thead>
                            <tbody>
                                <tr v-bind:key="patientHealthDatum.key" v-for="patientHealthDatum in patientHealthData">
                                    <td> {{ patientHealthDatum.id }} </td>
                                    <td> {{ patientHealthDatum.patientID }} </td>
                                    <td> {{ patientHealthDatum.temperature }} </td>
                                    <td> {{ patientHealthDatum.humidity }} </td>
                                    <td> {{ patientHealthDatum.pulseRate }} </td>
                                    <td> {{ patientHealthDatum.date }} </td>
                                    <td> {{ patientHealthDatum.time }} </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>

                <div v-else-if="retrievedOneData == false">
                    <div style="text-align: center; margin-top: 50px;">
                        <h3>Health records of all patients</h3>
                    </div>

                    <div style="overflow-x: auto;">
                        <table id="table">
                            <thead>
                                <th>S/N</th>
                                <th>Patient ID</th>
                                <th>Temperature</th>
                                <th>Humidity</th>
                                <th>Pulse Rate</th>
                                <th>Capture Date</th>
                                <th>Capture Time</th>
                            </thead>
                            <tbody>
                                <tr v-bind:key="allPatientHealthDatum.key" v-for="allPatientHealthDatum in allPatientHealthData">
                                    <td> {{ allPatientHealthDatum.id }} </td>
                                    <td> {{ allPatientHealthDatum.patientID }} </td>
                                    <td> {{ allPatientHealthDatum.temperature }} </td>
                                    <td> {{ allPatientHealthDatum.humidity }} </td>
                                    <td> {{ allPatientHealthDatum.pulseRate }} </td>
                                    <td> {{ allPatientHealthDatum.date }} </td>
                                    <td> {{ allPatientHealthDatum.time }} </td>
                                </tr>
                            </tbody>
                        </table>
                    </div>
                </div>
            </div>
        </div>
    </div>
</template>

<script>
    import axios from 'axios';

    export default {
        name: 'Home',

        data(){
            return {
                logo: 'IoT SYSTEM',
                token: '',
                patientID: '',
                patientHealthData: [],
                allPatientHealthData: [],
                retrievedOneData: null
            }
        },

        mounted(){
            try{
                this.token = localStorage.getItem('token');
            }catch(error){
                // DO NOTHING
            }
        },

        methods: {
            getPatientHealthDataByID: function(){
                this.retrievedOneData = null;

                const configHeaders = {
                    headers: {
                        'Authorization' : 'Bearer ' + this.token,
                        'Content-Type': 'application/json'
                    }
                };

                if(this.patientID !== ''){
                    axios.get('https://iot-health-monitoring-backend.herokuapp.com/api/v1/health-records', configHeaders)
                        .then(response => {
                            this.retrievedOneData = true;
                            this.allPatientHealthData = response.data.data;

                            this.patientHealthData = this.allPatientHealthData
                                .filter(patientHealthDatum => patientHealthDatum.patientID === this.patientID);
                            
                            if(this.patientHealthData.length == 0){
                                alert("There are no captured data for this patient");
                            }
                        })
                        .catch(e => {
                            alert(e);
                        })
                }else{
                    alert('Please, enter patient\'s ID.');
                }
            },

            getAllPatientHealthData: function(){
                this.retrievedOneData = null;

                const configHeaders = {
                    headers: {
                        'Authorization' : 'Bearer ' + this.token,
                        'Content-Type': 'application/json'
                    }
                };

                axios.get('https://iot-health-monitoring-backend.herokuapp.com/api/v1/health-records', configHeaders)
                    .then(response => {
                        this.retrievedOneData = false;
                        this.allPatientHealthData = response.data.data;

                        if(this.allPatientHealthData.length == 0){
                            alert("There are no captured data");
                        }
                    })
                    .catch(e => {
                        alert(e)
                    })
            },

            logout: function(){
                try{
                    localStorage.removeItem("IsHealthAdminLoggedIn");
                }catch(e){
                    // DO NOTHING
                }
                
                window.location.href = '/';
            }
        }
    }
</script>

<style scoped>
    *{
        box-sizing: border-box;
    }

    .container{
        width: 90%;
        height: 100%;
        margin: auto;
    }

    .top{
        width: 100%;
        height: 80px;
        background-color: #EEEEEE;
    }

    .btn-primary{
        background-color: rgb(30, 50, 73);
        color: #FFFFFF;
        border: none;
    }

    .top-inner{
        height: 100%;
        display: flex;
        justify-content: space-between;
        align-items: center;
    }

    .logout-btn{
        color: rgb(158, 35, 35);
        padding: 8px 18px;
        margin-left:10px;
        border-radius: 10px;
        border: 1px solid rgb(158, 35, 35);
    }

    .landing{
        display: flex;
        justify-content: center;
        align-items: center;
        color: #FFFFFF;
        flex-flow: column;
        line-height: 0.2em;

        width: 100%;
        height: 350px;
        background: linear-gradient(180deg, rgba(0, 0, 0, 0.75) 25.62%, rgba(0, 0, 0, 0.75) 100%), url('~@/assets/img/emr.jpg');
        background-repeat: no-repeat;
        background-position: center;
        background-size: cover
    }

    .landing p{
        letter-spacing: 0.2em;
    }

    .landing button{
        margin-top: 25px;
        border-radius: 10px;
        padding: 15px 35px;
        background-color: #1b4125;
        color: #FFFFFF;
        border: 0.5px solid #FFFFFF;
    }

    .main h4{
        padding: 15px;
        color: #555555;
        width: 70%;
        margin: auto;
        text-align: center;
    }

    .form-div{
        width: 50%;
        margin: 20px auto;
        padding: 20px;
        border: 1px solid #CCCCCC;
        border-radius: 10px;
    }

    .form-div input,  .form-div button{
        height: 40px;
        margin-top: 8px;
    }

    .form-div .id-field{
        width: 69%;
        float: left;
        padding: 10px;
    }

    .form-div .get-data-btn{
        width: 30%;
        float: right;
    }

    .form-div .get-all-data-btn{
        width: 100%;
    }

    table{
        width: 90%;
        margin: 5px auto;
    }

    table, th, tr, td{
        border: 1px solid #444444;
        border-collapse: collapse;
    }

    table thead th{
        background: #1d1d1d;
        color: #FFFFFF;
        border: 1px solid #FFFFFF;
    }

    th, td{
        padding: 8px;
    }

    @media screen and (max-width: 450px){
        .landing h1, .landing p{
            font-size: 1em;
        }

        .landing p{
            letter-spacing: normal;
        }
        .main h4{
            width: 90%;
        }

        .form-div{
            width: 90%;
        }
    }

</style>