<template>
  <div>
    <div id="cesiumContainer" class="map3d-contaner"></div>
  </div>
</template>
<script>
import * as Cesium from 'cesium'
import { initCesium } from '@/utils/cesiumPluginsExtends/index'


export default {
  mounted() {
    this.initMap()
  },
  methods: {
    async initMap() {
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
      // let tileset = this.c_viewer.scene.primitives.add(
      //   new Cesium.Cesium3DTileset({
      //     url: 'static/data/3DTiles/building/tileset.json',
      //   }),
      // )
      // tileset.style = new Cesium.Cesium3DTileStyle({
      //   color: {
      //     conditions: [
      //       ["${height} >= 300", "rgba(0, 149, 251, 0.3)"],
      //       ["${height} >= 200", "rgb(0, 149, 251, 0.3)"],
      //       ["${height} >= 100", "rgb(0, 149, 251, 0.3)"],
      //       ["${height} >= 50", "rgb(0, 149, 251, 0.3)"],
      //       ["${height} >= 25", "rgb(0, 149, 251, 0.3)"],
      //       ["${height} >= 10", "rgb(0, 149, 251, 0.3)"],
      //       ["${height} >= 5", "rgb(0, 149, 251, 0.3)"],
      //       ["true", "rgb(0, 149, 251, 0.3)"]
      //     ]
      //   }
      // })

      // this.c_viewer.flyTo(tileset)
      try {
        const tileset = await Cesium.Cesium3DTileset.fromUrl(
          "static/data/3DTiles/building/tileset.json"
        );
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
        this.c_viewer.scene.primitives.add(tileset);
        this.c_viewer.flyTo(tileset)
      } catch (error) {
        console.error(`Error creating tileset: ${error}`);
      }
      this.createModel();
    },
    getCustomMaterialLine(colors) {
      return new Cesium.Scene.PolylineFlowMaterialProperty({
        color: colors,
        duration: 200
      })
    },
    createModel() {
      const _self = this;
      var colors = [
        new Cesium.Color(77 / 255, 201 / 255, 255 / 255, 1),
        new Cesium.Color(255 / 255, 201 / 255, 38 / 255, 1),
        new Cesium.Color(221 / 255, 221 / 255, 221 / 255, 1)
      ];
      var startPoint = Cesium.Cartesian3.fromDegrees(104.081701757991, 30.627042558105988)

      for (var i = 0, len = 8; i < len; i++) {

        var endPoint = Cesium.Cartesian3.fromDegrees((Math.random() / 100) + 104.081701757991, (Math.random() / 100) + 30.627042558105988)

        var positions = this.math3d.getLinkedPointList(startPoint, endPoint, 100000, 50);

        var glowingLine = this.c_viewer.entities.add({
          polyline: {
            positions: positions,
            width: 5,
            material: _self.getCustomMaterialLine(colors[i % 3]),
          }
        });
      }
    }
  },
  beforeUnmount() {
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
