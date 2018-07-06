<template>
  <div id="cesiumContainer"></div>
</template>
<script>
  import Cesium from 'cesium/Cesium'

  export default {
    name: 'CesiumView',
    data () {
      return {
        viewer: undefined
      };
    },
    mounted(){
      this.viewer = new Cesium.Viewer('cesiumContainer', {
        baseLayerPicker: false,
        timeline: false,
        animation: false,
        imageryProvider: new Cesium.WebMapTileServiceImageryProvider({
          url: "http://t0.tianditu.com/img_w/wmts?service=wmts&request=GetTile&version=1.0.0&LAYER=img&tileMatrixSet=w&TileMatrix={TileMatrix}&TileRow={TileRow}&TileCol={TileCol}&style=default&format=tiles",
          layer: "tdtBasicLayer",
          style: "default",
          format: "image/jpeg",
          tileMatrixSetID: "GoogleMapsCompatible",
          show: false
        })
      });
      this.viewer._cesiumWidget._creditContainer.style.display='none';//去除Logo
      this.viewer.scene.globe.enableLighting = true;//添加太阳
      this.addMultiplePoints(10);
    },
    methods: {
      addMultiplePoints(num) {
        for (let i = 0; i < num; i++) { //利用for循环实现加多个点
          this.viewer.entities.add({  //加点
            position: Cesium.Cartesian3.fromDegrees(parseInt(Math.random()*40), parseInt(Math.random()*80)),
            name: '国家 : ', //点击描上的显示信息
            point: {
              color: Cesium.Color.YELLOW,
              pixelSize: 8
            }
          });
        }
      }
    }
  };
</script>
<style scoped>
  #cesiumContainer {
    width: 100%;
    height: 100%;
    margin: 0;
    padding: 0;
    overflow: hidden;
  }
</style>
