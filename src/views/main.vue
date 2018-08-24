<template>
  <div id="cesiumContainer">
  </div>
</template>
<script>
  import Cesium from 'cesium/Cesium'
  import InfoWindowOverlay from '@/Handle/map/InfoWindowOverlay'

  export default {
    name: 'CesiumView',
    data() {
      return {
        viewer: undefined
      };
    },
    mounted() {
      const _t = this;

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
      this.viewer._cesiumWidget._creditContainer.style.display = 'none';//去除Logo
      this.viewer._infoBox._container.style.display = 'none';//隐藏默认弹出框
      this.viewer.scene.globe.enableLighting = true;//添加太阳
      this.addMultiplePoints(10);

      let el = document.createElement('div');
      el.style.position = 'absolute';
      let overlay = new InfoWindowOverlay({
        element: el
      });
      overlay.setViewer(_t.viewer);
      this.viewer._element.appendChild(overlay.element);

      let handler3D = new Cesium.ScreenSpaceEventHandler(_t.viewer.scene.canvas);
      handler3D.setInputAction(function (movement) {
        //点击弹出气泡窗口
        let pick = _t.viewer.scene.pick(movement.position);
        if (pick && pick.id) {//选中某模型
          let cartographic = Cesium.Cartographic.fromCartesian(pick.id._position._value);//世界坐标转地理坐标（弧度）
          let point=[ cartographic.longitude / Math.PI * 180, cartographic.latitude / Math.PI * 180];//地理坐标（弧度）转经纬度坐标
          // let destination=Cesium.Cartesian3.fromDegrees(point[0], point[1], 3000.0);
          overlay.element.innerHTML = '<div style="background: #fff;">789</div>';
          overlay.setPosition(point);
          overlay.show();
        }else{
          overlay.close();
        }
      }, Cesium.ScreenSpaceEventType.LEFT_CLICK);
    },
    methods: {
      addMultiplePoints(num) {
        for (let i = 0; i < num; i++) { //利用for循环实现加多个点
          this.viewer.entities.add({  //加点
            position: Cesium.Cartesian3.fromDegrees(parseInt(Math.random() * 40), parseInt(Math.random() * 80)),
            name: '国家 : ', //点击描上的显示信息
            description:'',
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
