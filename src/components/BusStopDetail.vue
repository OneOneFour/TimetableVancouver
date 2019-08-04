<template>
    <div class="bus-stop" v-if="nextBuses">
        <h2 class="stop-name">{{stop.OnStreet}} @ {{stop.AtStreet}}</h2>
        <h3 v-for="bus in nextBuses" >{{bus.RouteNo}} to {{bus.RouteName}} arrving in {{bus.Schedules[0].ExpectedCountdown}} minutes</h3>
    </div>
</template>
<script>

export default {
    props:{
        stop:Object,
        api_key:String
    },
    data(){
        return {
            nextBuses:[]
        }
    },
    mounted(){
            //console.log(this.stop);
            const timesURL = `https://api.translink.ca/rttiapi/v1/stops/${this.stop.StopNo}/estimates?apikey=${this.api_key}`;
            fetch(timesURL,{headers:new Headers({'accept':"application/JSON"})})
            .then(response=>{
                return response.json();
            })
            .then(data=>{
                this.nextBuses = data;
                this.nextBuses.sort((a,b)=>{return a.Schedules[0].ExpectedCountdown - b.Schedules[0].ExpectedCountdown});
            });
    }
}
</script>
<style>
.stop-name{
    margin-bottom: 10px;
}
</style>
