<template>
    <div class="bus-selector">
        <h4 class="navigator" @click="currentStop--" :class="{'hide-space':currentStop <= 0}"> &#8249;</h4>
        <bus-stop-detail :stop="stop" :api_key="api_key" :class="getVisible(index)" v-for="(stop,index) in stops" :key="index"></bus-stop-detail>
        <h4 class="navigator" @click="currentStop++" :class="{'hide-space':currentStop >= stops.length - 1}">&#8250;</h4>
    </div>
</template>
<script>
import BusStopDetail from './BusStopDetail.vue'
export default {

    props:{
        stops:Array,
        api_key:String
    },
    data(){
        return {
            currentStop:0
        }
    },
    methods:{
        getVisible(index){
            return {hide:index != this.currentStop}
        }
    },
    components:{
        'bus-stop-detail':BusStopDetail
    }
}
</script>
<style>
.bus-selector{
    display:flex;
    flex-direction: row;
    justify-content: center;
    align-items: center;
    justify-content: space-between;
    margin: 50px 0;
    height:50vh;
}
.hide{
    display:none;
}
.hide-space{
    visibility: hidden;
}
.navigator{
    cursor: pointer;
    font-size:50pt;
    padding:10px;
    user-select: none;
}
</style>
