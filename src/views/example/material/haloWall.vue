<template>
  <div>
    <div id="cesiumContainer"
         class="map3d-contaner"></div>
  </div>
</template>
<script>
import * as Cesium from 'cesium'
import { initCesium } from '@/utils/cesiumPluginsExtends/index'

export default {
  mounted () {
    this.initMap()
  },
  methods: {
    async initMap () {
      const { viewer, material, graphics, math3d } = new initCesium(
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

      this.c_viewer = viewer

      this.material = material
      this.graphics = graphics
      this.math3d = math3d

      this.material.setDefSceneConfig()
      this.material.setBloomLightScene()
      const tileset = await Cesium.Cesium3DTileset.fromUrl(
        'static/data/3DTiles/building/tileset.json'
      );
      this.c_viewer.scene.primitives.add(tileset);

      tileset.style = new Cesium.Cesium3DTileStyle({
        color: {
          conditions: [
            ['${height} >= 300', 'rgba(0, 149, 251, 0.3)'],
            ['${height} >= 200', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 100', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 50', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 25', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 10', 'rgb(0, 149, 251, 0.3)'],
            ['${height} >= 5', 'rgb(0, 149, 251, 0.3)'],
            ['true', 'rgb(0, 149, 251, 0.3)']
          ]
        }
      })

      this.c_viewer.flyTo(tileset)

      this.createPolygonOne()
      this.createPolygonTwo()
    },
    createCustMaterialWall (
      imgUrl,
      colorVal,
      durationNum,
      countNum,
      freelyType
    ) {
      return this.material.getCustomMaterialWall({
        image: imgUrl,
        freely: freelyType,
        direction: '+',
        count: countNum,
        color: colorVal,
        duration: durationNum
      })
    },
    createPolygonOne () {
      let three = this.c_viewer.entities.add({
        name: 'aaaaa',
        wall: {
          positions: Cesium.Cartesian3.fromDegreesArrayHeights([
            104.07263175401185, 30.647622150198725, 500.0,
            104.06369117158526, 30.648834374000277, 500.0,
            104.06437182811021, 30.62274533905387, 500.0,
            104.07463538167119, 30.62285687644371, 500.0,
            104.07263175401185, 30.647622150198725, 500.0
          ]),
          material: this.createCustMaterialWall(
            'static/data/images/Textures/test1.png',
            Cesium.Color.BLUE,
            2000,
            0.0,
            'cross'
          )
        }
      })
    },
    createPolygonTwo () {
      let three2 = this.c_viewer.entities.add({
        name: 'aaaaa',
        wall: {
          positions: Cesium.Cartesian3.fromDegreesArrayHeights([
            104.09816110606057, 30.659821965447698, 200.0,
            104.1120972824757, 30.65330300319938, 200.0,
            104.10758419863926, 30.64592470850288, 200.0,
            104.09351691196979, 30.652434826507452, 200.0,
            104.09816110606057, 30.659821965447698, 200.0,
          ]),
          material: this.createCustMaterialWall(
            'static/data/images/Textures/color3.png',
            Cesium.Color.BLUE,
            2000,
            5,
            'vertical'
          )
        }
      })
    }
  },
  beforeUnmount () {
    this.c_viewer = null
    this.material = null
    this.graphics = null
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
