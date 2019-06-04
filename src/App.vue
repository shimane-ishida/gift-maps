<template>
    <div id=app>
      <div class="header">
        <csvhead v-on:child-event="parentMethod"></csvhead>
        <jsonhead v-on:child-event="parentMethod"></jsonhead>
      </div>
      <div id=map></div>
    </div>
</template>

<script>

import csvhead from './components/csvhead'
import jsonhead from './components/jsonhead'
import  'leaflet/dist/leaflet.css'
import  L from 'leaflet'
import { log } from 'util';

export default {
    data: function(){
      return {
        map: null,
        markerLayer:  L.featureGroup(),
        latlng: null
      }
    },
    mounted() {
        this.initMap();
        this.initLayer();
    },
    components: {
      csvhead,
      jsonhead
    },
    methods: {
      parentMethod: function(fromChildVal){
        this.latlng = JSON.parse(fromChildVal);
        this.clearLayers();
        this.addLayer();
      },
      initMap: function(){
        this.map = L.map( 'map', { center: L.latLng( 35.6825, 139.752778 ), zoom: 15 } ).addLayer(
            L.tileLayer( 'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png' ))
      },
      initLayer: function(){
        this.latlng = require('./assets/point.json');
        this.addLayer();
      },
      addLayer: function(){
        this.latlng.forEach(e => {
          let marker = L.marker([e.lat, e.lng],
            { 
              icon: L.divIcon( 
                {
                  className: this.inocColorValidation(e.iconColor),
                  iconSize: [ 16, 16 ],
                  draggable:false,
                  title:e.title
                }
              ),
            }
          )
          marker.bindPopup(e.title);
          marker.on('mouseover',function(){ marker.openPopup(); }); // マウスオーバー
          marker.on('mouseout', function() { marker.closePopup(); }); // マウスアウト
          this.markerLayer.addLayer(marker);
        });
        this.map.addLayer(this.markerLayer);
        this.fitBounds();
      },
      clearLayers: function(){
        this.markerLayer.clearLayers();
      },
      inocColorValidation: function(strColor){
        let resutlColor = "";
        switch (strColor){
          case 'red':
            resutlColor = "red marker";
            break;
          case 'bule':
            resutlColor = "bule marker";
            break;
          default:
            resutlColor = "red marker";
        }
        return resutlColor;
      },
      fitBounds: function(){
        this.map.fitBounds(this.markerLayer.getBounds());
      }
    }
}
</script>


<style>

html, body, #app{
  margin: 0;
  padding: 0;
}

#map{
  width: 90%;
  height: calc(100vh - 230px);
  margin: 10px auto;
  background: beige;
}

body { margin: 0; }

.header {
  width: 90%;
  margin: 20px auto;
}

.marker {
  text-align      : center;
  color           : white;
  font-size       : 16;
  border-radius   : 8px;
  box-shadow      : 8px 8px 8px rgba( 0, 0, 0, 0.4 );
}
.red {
  background      : red;
}
.bule {
  background      : blue;
}

</style>