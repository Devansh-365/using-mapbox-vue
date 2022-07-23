
<template>
    <div id="map" class="w-full h-screen"></div>
</template>
<script>

import mapboxgl from 'mapbox-gl'
import algoliasearch from 'algoliasearch';


const client = algoliasearch('8JO6B8QMWY', '969addb6985ef227919a7d9e96f47cd3');
const index = client.initIndex('user_data');

// Database of users
let records = []

// fetch all data from api
index.browseObjects({
  batch: batch => {
    records = records.concat(batch);
  },
  query: '',
}).then(() => console.log(records));


export default {
    head: {
      link: [
        {
          rel: 'stylesheet',
          href: 'https://api.mapbox.com/mapbox-gl-js/v1.10.0/mapbox-gl.css',
        },
      ],
    },
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
                center: [10.676524188468528, 55.88490923940639]
            })
            index.browseObjects({
            batch: batch => {
                records = records.concat(batch);
            },
            query: '',
            }).then(() => { for (const record of records) {
                // create a HTML element for each turbine
                const el = document.createElement('div');
                el.className = 'marker';

                // make a marker for each turbine and add to the map
                new mapboxgl.Marker(el)
                    .setLngLat([record["location.lng"], record["location.lat"]])
                    .setPopup(
                        new mapboxgl.Popup({ offset: 25 }) // add popups
                            .setHTML(
                                `<h3 class=""text-blue-500>${record.fullName}</h3><p>${record.id}</p>`
                            )
                    )
                    .addTo(this.map);
                console.log([record.fullName, record.fullName])
        }})
        }
    }
}
</script>

<style>
.mapboxgl-popup {
  max-width: 200px;
}

.mapboxgl-popup-content {
  text-align: center;
  font-family: 'Open Sans', sans-serif;
}
.marker {
  background-image: url('static/mapbox-icon.png');
  background-size: cover;
  width: 50px;
  height: 50px;
  border-radius: 50%;
  cursor: pointer;
}
</style>