
<template>
  <div id="cesiumContainer">
  </div>
  <el-dialog v-model="data.dialogTableVisible" :title="data.title">
    <div v-if="data.pickChild.peopleNum">
      <el-table :data="tableData" style="width: 100%">
        <el-table-column prop="业主" label="业主" />
        <el-table-column prop="单位" label="单位" />
        <el-table-column prop="楼层" label="楼层" />
      </el-table>
      <div style="float: right">人口：{{ data.pickChild.peopleNum }}</div>
    </div>
    <div v-else>业主：{{ data.pickChild.owner }}</div>
  </el-dialog>
</template>
<script setup>
import { onMounted } from 'vue'
import * as Cesium from 'cesium'
import { load3dtiles, update3dtiles } from './tool/load'
import { reactive } from 'vue'
import buildData from './assets/build.json'
const data = reactive({
  title: '',
  dialogTableVisible: false,
  pickChild: null
})
Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiJmMDY1ZmUxNi1mMGNhLTQ1NDEtYTU2YS0wNTUwZDJhOWNkNmEiLCJpZCI6MTE1MTg3LCJpYXQiOjE3MDA0NDUxNDB9.wdc1z2bc8UV2q_FpcuB5QKtRx8OYLKq4KI3gDs6-gA8'
let viewer
onMounted(() => {
  // viewer是API的开始
  viewer = new Cesium.Viewer('cesiumContainer', {
    infoBox: false,  //隐藏右边信息
    selectionIndicator: false //隐藏选中框
  })
  load3dtiles(viewer, '/src/assets/b3dm/tileset.json', (tileset) => {
    update3dtiles(tileset)
    viewer.zoomTo(tileset)
    initData()
    initHandler()
  })
})
const initData = () => {
  for (let key in buildData) {
    viewer.entities.add({
      id: key,
      position: Cesium.Cartesian3.fromDegrees(...buildData[key].center),
      polygon: {
        hierarchy: {
          positions: Cesium.Cartesian3.fromDegreesArray(buildData[key].range),
        },
        material: Cesium.Color.fromCssColorString(buildData[key].color).withAlpha(0.09)
      },
      polyline: {
        positions: Cesium.Cartesian3.fromDegreesArray(buildData[key].range),
        material: new Cesium.PolylineGlowMaterialProperty({
          glowPower: 0.1,
          color: Cesium.Color.fromCssColorString(buildData[key].color),
        }),
        width: 15,
        clampToGround: true//贴地
      },
      label: {
        text: buildData[key].type,
        font: '20px'
      }
    })
  }
}


const initHandler = () => {
  let lastEntity
  let handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas)
  handler.setInputAction((event) => {
    let pick = viewer.scene.pick(event.endPosition)
    if (pick && pick.id) {
      if (lastEntity) {
        lastEntity.polygon.material = Cesium.Color.fromCssColorString(buildData[lastEntity.id].color).withAlpha(0.09)
      }
      pick.id.polygon.material = Cesium.Color.RED.withAlpha(0.2)
      lastEntity = pick.id
    } else {
      if (lastEntity) {
        lastEntity.polygon.material = Cesium.Color.fromCssColorString(buildData[lastEntity.id].color).withAlpha(0.09)
      }
      lastEntity = null
    }
  }, Cesium.ScreenSpaceEventType.MOUSE_MOVE)
  handler.setInputAction((event) => {
    let pick = viewer.scene.pick(event.position)
    if (pick && pick.id) {
      if (!pick.id.name) {
        viewer.entities.removeAll()
        handler.removeInputAction(Cesium.ScreenSpaceEventType.MOUSE_MOVE)
        let view = buildData[pick.id.id].view
        const positionArr = view.cameraPosition.split(',').map((a) => Number(a))
        const oriArr = view.cameraOrt.split(',').map((a) => Number(a))
        viewer.camera.flyTo({
          destination: new Cesium.Cartesian3(...positionArr),
          orientation: {
            heading: oriArr[0],
            pitch: oriArr[1],
            roll: oriArr[2],
          }
        })
        addLabel(pick.id.id)
      } else {
        data.dialogTableVisible = true
        data.pickChild = pickPolygon.child[pick.id.id]
        data.title = pickPolygon.type + "_" + data.pickChild.name
      }
    }
  }, Cesium.ScreenSpaceEventType.LEFT_CLICK)
}

let pickPolygon
const addLabel = (id) => {
  pickPolygon = buildData[id]
  buildData[id].child.forEach((item, index) => {
    viewer.entities.add({
      id: index,
      position: Cesium.Cartesian3.fromDegrees(...item.position),
      name: index,
      label: {
        text: item.name,
        font: "20px",
        fillColor: Cesium.Color.WHITE,
        backgroundColor: Cesium.Color.BLACK.withAlpha(0.5),
        showBackground: true,
      }
    })
  })
}

</script>
<style>
#cesiumContainer {
  width: 100vw;
  height: 100vh;
  overflow: hidden;
}
.cesium-viewer-bottom {
  display: none;
}
.btn {
  position: absolute;
  left: 50px;
  top: 50px;
  z-index: 999;
}
.box-card {
  position: absolute;
  left: 30px;
  top: 30px;
  z-index: 999;
  width: 300px;
}
.text {
  margin: 10px;
  cursor: pointer;
}
.text:hover {
  color: blueviolet;
}
</style>
