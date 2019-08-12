<template>
  <div id="app">
    <template v-if="stops.length > 0">
        <h1>The next bus is...</h1>
        <bus-stop-selector v-bind:stops="stops" :api_key="api_key"></bus-stop-selector>
        <footer>Last updated at {{updatedTime}}</footer>
    </template>
    <template v-else>   
        <h2>Loading...</h2>
    </template>
    </div>
</template>

<script>
import BusStopSelector from "./components/BusStopSelector"



const api_key = "o52MYm2YA0zvFBN3LU9D";



export default {
    name: 'app',
    components: {
        "bus-stop-selector":BusStopSelector
        
    },
    data(){return {
        stops:[],
        api_key:api_key,
        message:"",
        lastUpdated: null
    }},
    computed:{
        updatedTime(){
            return this.lastUpdated.getHours() + ":" + ((this.lastUpdated.getMinutes() < 10)?"0":"")+this.lastUpdated.getMinutes();   
        }
    },
    methods:{
        updateAll(){
            if(navigator.geolocation){
                navigator.geolocation.getCurrentPosition((position)=>{
                
                    // TransLink API can't handle long coords
                    this.latitude = position.coords.latitude.toFixed(6);
                    this.longitude = position.coords.longitude.toFixed(6);

                    let queryURL = `https://api.translink.ca/rttiapi/v1/stops?apikey=${api_key}&lat=${this.latitude}&long=${this.longitude}`;
                    fetch(queryURL,{headers:new Headers({"accept":"application/JSON"})})
                    .then(response=>{
                        return response.json();
                    })
                    .then(data=>{
                        this.stops = data.filter(stop => stop.Routes.length > 0);
                    });
                    this.lastUpdated = new Date();
                })
            }
        }
    },
    mounted(){
        this.updateAll();
    }
}


</script>

<style>
@import url('https://fonts.googleapis.com/css?family=Lexend+Deca&display=swap');

*{
    box-sizing: border-box;
    margin:0;
    padding:0;
}
body {
  font-family:'Lexend Deca', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
  background:#3498db;   
  color:#ecf0f1;
}
</style>
