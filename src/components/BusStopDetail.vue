<template>
    <div class="bus-stop">
        <h2 class="stop-name">{{stop.OnStreet}} @ {{stop.AtStreet}}</h2>
        <template v-if="nextBuses.length > 0">
            <h3 v-for="bus in nextBuses" >{{bus.RouteNo}} to {{bus.Schedules[0].Destination}} arrving in {{bus.Schedules[0].ExpectedCountdown}} minutes</h3>
        </template>
        <template v-else>
            <h3>Loading... </h3>
        </template>
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
            nextBuses:[],
            timeOnLoad: Date.now()
        }
    },
    methods:{
        refreshTimes(){
            const timesURL = `https://api.translink.ca/rttiapi/v1/stops/${this.stop.StopNo}/estimates?apikey=${this.api_key}`;
            fetch(timesURL,{headers:new Headers({'accept':"application/JSON"})})
            .then(response=>{
                return response.json();
            })
            .then(data=>{
                this.nextBuses = data.filter(bus=>bus.Schedules[0].ExpectedCountdown > 0);
                this.nextBuses.sort((a,b)=>{return a.Schedules[0].ExpectedCountdown - b.Schedules[0].ExpectedCountdown});
                this.timeOnLoad = Date.now();
            });
        }
    },
    mounted(){
        this.refreshTimes();
    }
}
</script>
<style>
.stop-name{
    margin-bottom: 10px;
}
.bus-stop{
    height:100%;
}
</style>
