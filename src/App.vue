<template>
    <div id=app>
      <div class="container">
        <csvhead v-on:child-event="parentMethod"></csvhead>
        <jsonhead v-on:child-event="parentMethod"></jsonhead>
      </div>
      <div class="container">
        <div class="menu">
          <div class="search">
            <label for="search_box">名称検索：</label>
            <input type="text" id="search_box" v-model="searchWord">
            <input type="button" value="クリア" v-on:click="searchClear">
            <p>検索結果：{{ msg }}</p>
          </div>
          <div class="tbl-01">
            <table class="tbl">
              <tr>
                <th>名称</th>
                <th>コンテンツ</th>
              </tr>
              <tr v-for="(val) in items" v-bind:key="val.id">
                <td>{{ val.title }}</td>
                <td ><span v-html="val.title"></span></td>
              </tr>
            </table>
          </div>
        </div>
      <div id=map></div>
      </div>
    </div>
</template>

<script>

import csvhead from './components/csvhead'
import jsonhead from './components/jsonhead'
import 'leaflet/dist/leaflet.css'
import L from 'leaflet'
import Underscore from 'underscore'

export default {
    data: function(){
      return {
        map: null,
        markerLayer: L.featureGroup(),
        items: null,
        latlng: null,
        searchWord: '',
        msg:'',
      }
    },
    mounted() {
        this.initMap();
        this.initLayer();
    },
    watch: {
      searchWord: {
        handler: function(){
          this.findBy(this.searchWord);
        },
        deep: true
      }
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
      addLayer: function(data = this.latlng){
        if(data.length === 0) {
          this.items = this.latlng;
          this.msg = "N/A";
          return;
        }
        this.items = data;
        data.forEach(e => {
          let marker = L.marker([e.lat, e.lng],
            { 
              icon: L.divIcon( 
                {
                  className: this.iconColorValidation(e.iconColor),
                  iconSize: [ 16, 16 ],
                  draggable: false,
                  title:e.title
                }
              ),
            }
          )
          marker.bindPopup("名称:"+e.title + "<br />本文：" + e.contents);
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
      iconColorValidation: function(strColor){
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
      },
      findBy: function(query){
        if(query === '') return;
        const result = Underscore._.filter(this.latlng,{title:query});
        this.msg = result.length;
        this.clearLayers();
        this.addLayer(result);
      },
      searchClear: function(){
        this.msg = '';
        this.searchWord = '';
        this.clearLayers();
        this.addLayer();
      }
    }
}
</script>

<style>
.container {
  display: flex;
  margin-bottom: 10px;
}

.file_head {
  background: beige;
  padding: 20px;
  margin-right: 20px
}

.menu {
  flex-wrap: wrap;
}

.search > input[type="button"]{
  float: right;
  margin-top: 10px;
}

.search {
  background: beige;
  padding: 10px 20px 5px 20px;
  margin-right: 20px;
}

.search input[type="text"]{
  padding: 3px;
  width: 90%;
  margin: 0px 0px 0px 0px;
}

#map {
  width: 90%;
  height: calc(100vh - 230px);
  margin: 0 auto;
}

.marker {
  text-align: center;
  color: white;
  font-size: 16;
  border-radius: 8px;
  box-shadow: 8px 8px 8px rgba(0, 0, 0, 0.4);
}
.red {
  background: red;
}

.bule {
  background: blue;
}

table {
  margin: 20px auto;
}

.tbl-01 {
  height: calc(100vh - 340px);
  overflow: auto;
}

.tbl th {
  background: #686866;
  border: solid 1px #ccc;
  color: #fff;
  padding: 10px;
}

.tbl td {
  border: solid 1px #ccc;
  padding: 10px;
}

</style>