<template>
  <div class="sky-box">
    <div id="cesiumContainer"
         class="map3d-contaner"></div>

    <div class="position-panel">
      <el-card class="box-card">
        <div class="clearfix">
          <span>相机位置</span>
        </div>
        <div class="card-content">
          <p><span>longitude：</span>{{posLon}}</p>
          <p><span>latitude：</span>{{posLat}}</p>
          <p><span>heading：</span>{{posHeading}}</p>
          <p><span>pitch：</span>{{posPitch}}</p>
          <p><span>roll：</span>{{posRoll}}</p>
          <p><span>posAlt：</span>{{posAlt}}</p>
        </div>
        <div class="clearfix">
          <span>相机笛卡尔位置</span>
        </div>
        <div class="card-content">
          <p><span>笛卡尔经度:</span>{{ cartesinaX }}</p>
          <p><span>笛卡尔纬度:</span>{{cartesinaY}}</p>
          <p><span>笛卡尔高度:</span>{{ cartesinaZ }}</p>
          <p><span>朝向:</span>{{cartesinaH}}</p>
          <p><span>倾斜角:</span>{{cartesinaP}}</p>
          <p><span>翻滚角:</span>{{cartesinaR}}</p>
        </div>
      </el-card>
    </div>
    <div class="position-panel-right">
      <el-card class="box-card">
        <div class="clearfix"><span>左键点击，不同的坐标拾取</span></div>
        <div class="group-btn">
          <el-button size="mini"
                     @click="btnGroupOpera('PickElliposid')">与球面求交</el-button>
          <el-button size="mini"
                     @click="btnGroupOpera('scenePickPos')">scenePick拾取场景坐标</el-button>
          <el-button size="mini"
                     @click="btnGroupOpera('scenePickBuild')">拾取三维物体</el-button>
          <el-button size="mini"
                     @click="btnGroupOpera('drillPick')">drillPick拾取多个物体</el-button>
          <el-button size="mini"
                     @click="btnGroupOpera('globePick')">globePick拾取坐标位置</el-button>
          <el-button size="mini"
                     @click="btnGroupOpera('getImg')">拾取img</el-button>
        </div>
      </el-card>
    </div>
  </div>
</template>
<script>
import * as Cesium from 'cesium'
import { initCesium } from '@/utils/cesiumPluginsExtends/index'
let cHander = null;

export default {
  data () {
    return {
      posHeading: 0,
      posPitch: 0,
      posRoll: 0,
      posAlt: 0,
      posLat: 0,
      posLon: 0,
      cartesinaX: 0,
      cartesinaY: 0,
      cartesinaZ: 0,
      cartesinaH: 0,
      cartesinaP: 0,
      cartesinaR: 0
    }
  },
  mounted () {
    this.initMap()
  },
  methods: {
    async initMap () {
      const tempData = [
        {
          type: 'UrlTemplateImageryProvider',
          option: {
            url: 'https://webst02.is.autonavi.com/appmaptile?style=6&x={x}&y={y}&z={z}'
          }
        },
        {
          type: 'UrlTemplateImageryProvider',
          option: {
            url: 'https://webst03.is.autonavi.com/appmaptile?x={x}&y={y}&z={z}&style=7',
          }
        }
      ]
      const { viewer,
        base
      } = new initCesium(
        {
          cesiumGlobal: Cesium,
          containerId: 'cesiumContainer',
          viewerConfig: {
            infoBox: false,
            shouldAnimate: true,
          },
          extraConfig: {
            depthTest: true // 深度测试
          },
          MapImageryList: tempData
        })

      this.c_viewer = viewer;
      this.base = base;

      const tileset = await Cesium.Cesium3DTileset.fromUrl(
        "static/data/3DTiles/building/tileset.json"
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
            ['true', 'rgb(0, 149, 251, 0.3)'],
          ],
        },
      })
      viewer.flyTo(tileset)
      this.initCameraPos();
      this.cameraModify();
    },
    initCameraPos () {
      let position = this.base.getCameraPosition();
      if (position) {
        const { lon,
          lat,
          height,
          heading,
          pitch,
          roll,
          position: { x, y, z },
          cameraHeading,
          cameraPitch,
          cameraRoll,
        } = position;
        this.posAlt = height;
        this.posHeading = heading;
        this.posPitch = pitch;
        this.posRoll = roll;
        this.posLat = lat;
        this.posLon = lon;

        this.cartesinaX = x;
        this.cartesinaY = y;
        this.cartesinaZ = z;
        this.cartesinaH = cameraHeading;
        this.cartesinaP = cameraPitch;
        this.cartesinaR = cameraRoll;
      } else {
        console.log('can not get postion.')
      }
    },
    cameraModify () {
      this.c_viewer.scene.camera.moveEnd.addEventListener((move) => {
        let position = this.base.getCameraPosition()
        if (position) {
          const { lon,
            lat,
            height,
            heading,
            pitch,
            roll,
            position: { x, y, z },
            cameraHeading,
            cameraPitch,
            cameraRoll,
          } = position;
          this.posAlt = height;
          this.posHeading = heading;
          this.posPitch = pitch;
          this.posRoll = roll;
          this.posLat = lat;
          this.posLon = lon;

          this.cartesinaX = x;
          this.cartesinaY = y;
          this.cartesinaZ = z;
          this.cartesinaH = cameraHeading;
          this.cartesinaP = cameraPitch;
          this.cartesinaR = cameraRoll;
        } else {
          console.log('can not get postion.')
        }
      });
    },
    // 绑定事件到场景中
    bindEventToScene (callbackFn) {
      cHander && cHander.removeInputAction(Cesium.ScreenSpaceEventType.LEFT_CLICK);
      this.base.bindHandelEvent({
        leftClick: (clickEvent, hander) => {
          cHander = hander;
          callbackFn && callbackFn(clickEvent)
        }
      })
    },
    /**
     * 按钮组操作事件
     * 如果获取所有位置点可以使用
     *  base.getCatesian3FromPX(e.position);
     * @param {*} type 
     */
    btnGroupOpera (type) {
      const scene = this.c_viewer.scene;
      const _self = this;
      if (type === 'scenePickBuild') {
        this.bindEventToScene(e => {
          // 拾取三维场景的物体
          let pickedFeature = scene.pick(e.position);
          if (!Cesium.defined(pickedFeature)) {
            this.$message('nothing picked!')
            console.log("nothing picked!")
          } else {
            this.$message('拾取到模型==》请控制台查看')
            console.log("this is obation build=>", pickedFeature)
          }
        })
      } else if (type === 'scenePickPos') {
        this.bindEventToScene(e => {
          // 用来拾取三维空间笛卡尔坐标
          let pickPosition = scene.pickPosition(e.position);
          this.c_viewer.entities.add({
            position: pickPosition,
            point: {
              color: Cesium.Color.RED,
              pixelSize: 50,// 像素大小
              outlineColor: Cesium.Color.YELLOWGREEN,
              outlineWidth: 2,// 边框
              heightReference: Cesium.HeightReference.CLAMP_TO_GROUND
            }
          })
        })
      } else if (type === 'drillPick') {
        this.bindEventToScene(e => {
          // 用来拾取三维空间物体
          let pickedFeature = scene.drillPick(e.position, 2);
          this.$message('拾取到模型==》请控制台查看')
          console.log("this is obation build=>", pickedFeature)
        })
      } else if (type === 'PickElliposid') {
        this.bindEventToScene(e => {
          // 与球面求交,获取点击的球面位置
          let pickPositionElliposid = scene.camera.pickEllipsoid(e.position, scene.globe.elliposid);
          this.c_viewer.entities.add({
            position: pickPositionElliposid,
            point: {
              color: Cesium.Color.RED,
              pixelSize: 50,// 像素大小
              outlineColor: Cesium.Color.YELLOWGREEN,
              outlineWidth: 2,// 边框
              heightReference: Cesium.HeightReference.CLAMP_TO_GROUND
            }
          })
        })
      } else if (type === 'globePick') {
        this.bindEventToScene(e => {
          let ray = this.c_viewer.camera.getPickRay(e.position);//创建一条于地球的射线
          // 与地球globe求交,获取点击的球面位置
          let globePickPos = scene.globe.pick(ray, scene);
          // 将笛卡尔转为经纬度
          let lonlat = Cesium.Cartographic.fromCartesian(globePickPos);
          console.log('lon---lonlat', lonlat)
          this.c_viewer.entities.add({
            position: globePickPos,
            point: {
              color: Cesium.Color.RED,
              pixelSize: 50,// 像素大小
              outlineColor: Cesium.Color.YELLOWGREEN,
              outlineWidth: 2,// 边框
              heightReference: Cesium.HeightReference.CLAMP_TO_GROUND
            }
          })
        })
      } else if (type === 'getImg') {
        this.bindEventToScene(e => {
          // 获取img
          let pickRayImg = this.c_viewer.camera.getPickRay(e.position);
          let fePromise = this.c_viewer.imageryLayers.pickImageryLayerFeatures(pickRayImg, scene)
          if (!Cesium.defined(fePromise)) {
            this.$message('nothing picked!')
            console.log("nothing picked!")
          } else {
            Cesium.when(fePromise, function (fea) {
              // 获取到突出
              if (fea.length > 0) {
                _self.$message('拾取到模型==》请控制台查看')
                console.log("this is obation build=>", fea)
              } else {
                _self.$message('未拾取到物体')
              }
            })

          }
        })
      }
    }
  },
  beforeUnmount () {
    this.c_viewer = null;
    this.base = null;
  }
}
</script>
<style lang="scss" scoped>
.sky-box {
  position: relative;
  .map3d-contaner {
    width: 100%;
    height: 100%;
    overflow: hidden;
  }
  .position-panel {
    position: absolute;
    top: 10px;
    left: 20px;
    z-index: 3;
    .card-content {
      p {
        span {
          color: #808080;
        }
      }
    }
  }
  .position-panel-right {
    position: absolute;
    top: 10px;
    right: 20px;
    z-index: 3;
    .group-btn {
      display: flex;
      flex-direction: column;
      button {
        margin-top: 4px;
        margin-left: 10px;
      }
    }
  }
}
</style>
