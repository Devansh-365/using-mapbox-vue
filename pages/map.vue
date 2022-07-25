
<template>
    <div id="map" class="w-full h-screen"></div>
</template>
<script>

import mapboxgl from 'mapbox-gl'
import algoliasearch from 'algoliasearch';
import MapboxGeocoder from '@mapbox/mapbox-gl-geocoder'
import { env } from 'process';


const client = algoliasearch('8JO6B8QMWY', '969addb6985ef227919a7d9e96f47cd3');
const index = client.initIndex('user_data');

// Database of users
let records = []

// fetch all data from api
// index.browseObjects({
//   batch: batch => {
//     records = records.concat(batch);
//   },
//   query: '',
// }).then(() => console.log(records));


export default {
    head: {
      link: [
        {
          rel: 'stylesheet',
          href: 'https://api.mapbox.com/mapbox-gl-js/v1.10.0/mapbox-gl.css',
        },
        {
          rel: 'stylesheet',
          href: 'https://api.mapbox.com/mapbox-gl-js/plugins/mapbox-gl-geocoder/v5.0.0/mapbox-gl-geocoder.css'
        }
        
      ],
      
    },
    data(){
        return{
            access_token: "pk.eyJ1IjoiZGV2YW5zaDM2NSIsImEiOiJjbDV5YnV2dW0wY2VlM2RwMjYzZzY1anFyIn0.5njP9f5rkA5KNHRmlALP_A",
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
                zoom: 3,
                center: [10.676524188468528, 55.88490923940639]
            })
            index.browseObjects({
            batch: batch => {
                records = records.concat(batch);
            },
            query: '',
            }).then(() => { for (const record of records) {
                // create a HTML element for each user
                const el = document.createElement('div');
                el.className = 'marker';

                // make a marker for each user and add to the map
                new mapboxgl.Marker(el)
                    .setLngLat([record["location.lng"], record["location.lat"]])
                    .setPopup(
                        new mapboxgl.Popup({ offset: 25 }) // add popups
                            .setHTML(
                                `<img src="/static/mapbox-icon.png"><h2 class=""text-blue-500>${record.fullName}</h2><p>${record.companyName}</p>`
                            )
                  ).addTo(this.map);
        }})

        function forwardGeocoder(query) {
          const matchingFeatures = [];
          index.browseObjects({
            batch: batch => {
                records = records.concat(batch);
            },
            query: '',
            }).then(() => {for (const record of records) {
          if (
          record.fullName
          .toLowerCase()
          .search(query.toLowerCase()) !== -1
          ) {
          record['place_name'] = `🌲 ${record.fullName}`;
          record['center'] = `[${record["location.lng"]}, ${record["location.lat"]}]`;
          record['place_type'] = record["location.country"];
          console.log(record)
          matchingFeatures.push(record);
          }
          }
          return Promise.resolve(matchingFeatures)

            })}
        
        // Initialize the geocoder
        const geocoder = new MapboxGeocoder({
          accessToken: mapboxgl.accessToken,
          localGeocoderOnly: true,
          localGeocoder: forwardGeocoder,
          mapboxgl: mapboxgl, 
          
          placeholder: 'Search users', 
          });

          geocoder.addTo(this.map)
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