<template>
  <div>
    <div id="cesiumContainer" class="map3d-contaner"></div>
  </div>
</template>
<script>
import * as Cesium from 'cesium'
import { Material } from '@/utils/cesiumPluginsExtends/singleImport/Material'
import { raod, raod2, raod3, raod4, raod5, raod6, raod7 } from './extraData'

export default {
  mounted() {
    this.initMap()
  },
  methods: {
    initMap() {
      const materialObj = new Material({
        cesiumGlobal: Cesium,
        containerId: 'cesiumContainer',
        viewerConfig: {
          infoBox: false,
          shouldAnimate: true,
        },
        extraConfig: {},
        MapImageryList: []
      })

      this.c_viewer = materialObj.viewer;
      this.material = materialObj.material;

      this.material.setDefSceneConfig()
      this.material.setBloomLightScene()
      this.load3dTiles(materialObj.viewer);
    },
    async load3dTiles(viewer) {
      var _self = this;
      viewer.scene.sun.show = false;
      viewer.scene.moon.show = false;
      viewer.scene.skyAtmosphere.show = false;


      try {
        const tileset = await Cesium.Cesium3DTileset.fromUrl('static/data/3DTiles/building/tileset.json');
        viewer.scene.primitives.add(tileset);
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
        });
        viewer.flyTo(tileset)
        _self.addEntityToScene(viewer);
      } catch (err) {
        console.log(err);
        ElMessage.error('加载3dtiles失败');
      }
    },
    addEntityToScene(viewer) {

      viewer.entities.add({
        polyline: {
          positions: this.material.transformWGS84ArrayToCartesianArray(raod),
          width: 10,
          material: new Cesium.Scene.PolylineCityLinkMaterialProperty({
            color: new Cesium.Color(5.0, 5.0, 10.0),
            duration: 2000
          }),
        }
      });
      viewer.entities.add({
        polyline: {
          positions: this.material.transformWGS84ArrayToCartesianArray(raod2),
          width: 10,
          material: new Cesium.Scene.PolylineCityLinkMaterialProperty({
            color: new Cesium.Color(5.0, 5.0, 10.0),
            duration: 2000,
          }),
        }
      });
      viewer.entities.add({
        polyline: {
          positions: this.material.transformWGS84ArrayToCartesianArray(raod3),
          width: 10,
          material: new Cesium.Scene.PolylineCityLinkMaterialProperty({
            color: new Cesium.Color(5.0, 5.0, 10.0),
            duration: 2000
          }),
        }
      });
      viewer.entities.add({
        polyline: {
          positions: this.material.transformWGS84ArrayToCartesianArray(raod4),
          width: 10,
          material: new Cesium.Scene.PolylineCityLinkMaterialProperty({
            color: Cesium.Color.fromBytes(255, 165, 0, 1.0),
            duration: 2000
          }),
        }
      });
      viewer.entities.add({
        polyline: {
          positions: this.material.transformWGS84ArrayToCartesianArray(raod5),
          width: 10,
          material: new Cesium.Scene.PolylineCityLinkMaterialProperty({
            color: Cesium.Color.fromBytes(220, 20, 60, 1.0),
            duration: 2000
          }),
        }
      });
      viewer.entities.add({
        polyline: {
          positions: this.material.transformWGS84ArrayToCartesianArray(raod6),
          width: 10,
          material: new Cesium.Scene.PolylineCityLinkMaterialProperty({
            color: new Cesium.Color(5.0, 5.0, 8.0),
            duration: 2000,

          }),
        }
      });

      viewer.entities.add({
        polyline: {
          positions: this.material.transformWGS84ArrayToCartesianArray(raod7),
          width: 4,
          material: new Cesium.Scene.PolylineCityLinkMaterialProperty({
            color: Cesium.Color.fromBytes(255, 140, 0, 1),
            duration: 2000,
            imgUrl: 'static/data/images/Textures/colors1.png'
          }),
        }
      });
    }
  },
  beforeUnmount() {
    this.c_viewer = null;
    this.material = null;
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
