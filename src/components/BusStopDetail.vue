<template>
    <div class="bus-stop">
        <h2 class="stop-name">{{stop.OnStreet}} @ {{stop.AtStreet}}</h2>
        <div class="busses" v-if="nextBuses.length > 0">
            <h3 v-for="(bus,i) in nextBuses" >{{bus.RouteNo | stripzeros}} to {{bus.Schedules[0].Destination}} arriving {{bus.Schedules[0].ExpectedCountdown | asCountdown}} </h3>
        </div>
        
        <!-- <template v-else>
            <div class="busses">
                <h3 style="text-align:center">Loading... </h3>
            </div>
        </template> -->
    </div>
</template> 
<script>
const a  = {
                    RouteNo:"114",
                    Destination:"FUCK YOU",
                    Schedules:[
                        {Destination:"Fuckland",ExpectedCountdown:13}

                    ]
                };
let ticktock = null;
export default {
    props:{
        stop:Object,
        api_key:String
    },
    data(){
        return {
            nextBuses:[],
            timeOnLoad: null
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
                this.nextBuses = data.filter(bus=>bus.Schedules[0].ExpectedCountdown >= 0);
                this.nextBuses.sort((a,b)=>{return a.Schedules[0].ExpectedCountdown - b.Schedules[0].ExpectedCountdown});
                this.timeOnLoad = new Date();
            });
            setInterval(function(){
                let newBus = []
                for(let i=0; i < this.nextBuses.length;i++){
                    let bus = this.nextBuses[i];
                    for(let j=0; j <bus.Schedules.length; j++){
                        bus.Schedules[j].ExpectedCountdown -= 1;
                    }
                    newBus.push(bus);
                    newBus[i].Schedules = newBus[i].Schedules.filter((s)=>s.ExpectedCountdown >= 0);
                    
                }
                newBus.sort((a,b)=>{return a.Schedules[0].ExpectedCountdown - b.Schedules[0].ExpectedCountdown});
                this.nextBuses= newBus;
            }.bind(this),60*1000);
        }
    },
    filters:{
        asCountdown(val){
            if(val == 0){
                return "now!";
            }
            return "in " + val + " minute" + ((val > 1)? "s":"");
        },
        stripzeros(val){
            return val.split("").filter(c=> c != "0").join("");
        }
    },
    mounted(){
        this.refreshTimes();
    }
}
</script>
<style>
.stop-name{
    
    background:#27ae60;
    color:white;
    padding:10px 10px;
}
.busses{
    padding: 10px 6px;
    font-weight:lighter;
}
.busses h3:first-child{
    font-weight: 1000;
}
.busses h3{
    font-weight: lighter;
    font-size:14pt;
    margin: 3px 0;
    text-align: left;
    margin-left:10px;
}
.bus-stop{
    min-width: 30vw;
    min-height:20vh;
    
    margin: 0px 15px;
    background:#ffffff;
    color:#2980b9;
    border-radius: 10px;
    box-shadow: 2px 2px 10px 5px #2980b9;
}
</style>
