<template>
  <div>
    <div id="cesiumContainer"
         class="map3d-contaner"></div>
  </div>
</template>
<script >
import * as Cesium from 'cesium'
import { initCesium } from '@/utils/cesiumPluginsExtends/index'


export default {
  mounted () {
    this.initMap()
  },
  methods: {
    async initMap () {
      const { viewer,
        material,
        graphics,
        math3d } = new initCesium(
          {
            cesiumGlobal: Cesium,
            containerId: 'cesiumContainer',
            viewerConfig: {
              infoBox: false,
              shouldAnimate: true,
            },
            extraConfig: {},
            MapImageryList: []
          })


      this.c_viewer = viewer;

      this.material = material;
      this.graphics = graphics;
      this.math3d = math3d;

      this.material.setDefSceneConfig()
      this.material.setBloomLightScene()
      const tileset = await Cesium.Cesium3DTileset.fromUrl(
        'static/data/3DTiles/building/tileset.json'
      );
      this.c_viewer.scene.primitives.add(tileset);

      tileset.style = new Cesium.Cesium3DTileStyle({
        color: {
          conditions: [
            ["${height} >= 300", "rgba(0, 149, 251, 0.3)"],
            ["${height} >= 200", "rgb(0, 149, 251, 0.3)"],
            ["${height} >= 100", "rgb(0, 149, 251, 0.3)"],
            ["${height} >= 50", "rgb(0, 149, 251, 0.3)"],
            ["${height} >= 25", "rgb(0, 149, 251, 0.3)"],
            ["${height} >= 10", "rgb(0, 149, 251, 0.3)"],
            ["${height} >= 5", "rgb(0, 149, 251, 0.3)"],
            ["true", "rgb(0, 149, 251, 0.3)"]
          ]
        }
      })

      this.flyto();

      this.createModel();
    },
    flyto () {
      this.material.flyTo({
        position: { x: -1337132.0092982147, y: 5330611.474631115, z: 3228680.029449292 },
        orientation: {
          heading: Cesium.Math.toRadians(1.0114629015290062),
          pitch: Cesium.Math.toRadians(-23.53661660731824),
          roll: Cesium.Math.toRadians(0.00324596311071617)
        }
      })
    },
    createModel () {
      let colors = [
        new Cesium.Color(77 / 255, 201 / 255, 255 / 255, 1),
        new Cesium.Color(255 / 255, 201 / 255, 38 / 255, 1),
        new Cesium.Color(221 / 255, 221 / 255, 221 / 255, 1)
      ];
      for (let i = 0, len = 8; i < len; i++) {

        let lon = (Math.random() / 100) + 104.081701757991
        let lat = (Math.random() / 100) + 30.627042558105988
        let start = Cesium.Cartesian3.fromDegrees(lon, lat, 0)
        let stop = Cesium.Cartesian3.fromDegrees(lon, lat, 10000)
        this.c_viewer.entities.add({
          polyline: {
            positions: [start, stop],
            width: 3,
            material: new Cesium.Scene.PolylineCityLinkMaterialProperty({
              color: colors[i % 3],
              duration: 1500
            }),
          }
        });
      }
    }
  },
  beforeUnmount () {
    this.c_viewer = null;
    this.material = null;
    this.graphics = null;
  }
}
</script>
<style lang="scss" scoped>
.map3d-contaner {
  width: 100%;
  height: 100%;
  overflow: hidden;
  .ctrl-group {
    position: absolute;
    top: 10px;
    right: 20px;
    z-index: 999;
    .reset-home-btn {
      color: #36a3f7;
      cursor: pointer;
    }
  }
}
</style>
