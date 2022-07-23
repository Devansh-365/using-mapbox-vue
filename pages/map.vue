<template>
    <div id="map" class="w-full h-screen"></div>
</template>
<script>

import mapboxgl from 'mapbox-gl'
export default {
    data(){
        return{
            access_token: 'pk.eyJ1IjoiZGV2YW5zaDM2NSIsImEiOiJjbDV5YnV2dW0wY2VlM2RwMjYzZzY1anFyIn0.5njP9f5rkA5KNHRmlALP_A',
            map:{}
        }
    },
    mounted(){
            this.createMap()
        },
    methods: {
        createMap(){
            mapboxgl.accessToken = this.access_token
            this.map = new mapboxgl.Map({
                container: 'map',
                style: 'mapbox://styles/mapbox/streets-v11',
                zoom: 11,
                center: [107.61861,-6.90389]
            })
        }
    }
}
</script>

</style>