<template>
  <div id="cesiumContainer">

  </div>
  <el-dialog v-model="data.dialogVisible" :title="data.title" width="800">
  </el-dialog>
</template>

<script setup>
import * as Cesium from 'cesium'
// console.log(Cesium)
//组件先挂载到网页上，地图在实例化到组件中
import { onMounted, reactive } from 'vue'
//引入turf
// import * as turf from '@turf/turf'
//引入tools中的load中的方法
import { load3dtiles, update3dtiles } from './tools/load'
//引入数据
import buildDate from '/src/assets/build.json'

// import { polygon } from '@turf/turf'
// import Polyline from 'cesium/Source/Scene/Polyline'
// import Label from 'cesium/Source/Scene/Label'

//设置cesium ion的token
Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI3NGFlY2YzZi1mYWU1LTRmNTEtYTIwYy05N2FjY2M0NDgyOWYiLCJpZCI6MjA2ODkwLCJpYXQiOjE3MTIzODYxOTZ9.ZlcLWXOr3z6gTIkTrYfjdYB6rrW0Wu5mPAW3jE8OacU'

//在外面申明比较好,不然会报错
let viewer
const data = reactive({
  arr: [],
  dialogVisible: false,
  title: '',
})

onMounted(() => {
  //是操作地图开始的API
  viewer = new Cesium.Viewer('cesiumContainer', {
    infoBox: false,//关闭信息框
    selectionIndicator: false,//关闭选中指示器
  })
  load3dtiles(viewer, '/src/assets/b3dm/tileset.json', (tileset) => {
    update3dtiles(tileset)//更新3dtiles
    viewer.zoomTo(tileset)//缩放到3dtiles
    initDate()//初始化数据
    initHandler()//初始化事件
  })
})

const initDate = () => {
  // console.log(buildDate)
  for (let key in buildDate) {
    viewer.entities.add({
      id: key,
      position: Cesium.Cartesian3.fromDegrees(...buildDate[key].center),
      polygon: {
        hierarchy: Cesium.Cartesian3.fromDegreesArray(buildDate[key].range),
        material: Cesium.Color.fromCssColorString(buildDate[key].color).withAlpha(0.09) //颜色
      },
      polyline: {
        positions: Cesium.Cartesian3.fromDegreesArray(buildDate[key].range),
        material: new Cesium.PolylineGlowMaterialProperty({
          glowPower: 0.1,
          color: Cesium.Color.fromCssColorString(buildDate[key].color)
        }),
        clampToGround: true,//贴地
        width: 15,//宽度
      },
      label: {
        text: buildDate[key].type,
        font: '20px',
      }
    })
  }
}
const initHandler = () => {
  let lastEntity
  let handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas)//监听鼠标事件
  handler.setInputAction((event) => {
    let pick = viewer.scene.pick(event.endPosition)//获取鼠标移动的位置
    // console.log(pick)
    if (pick && pick.id) {
      if (lastEntity) {
        //console.log(lastEntity.id)
        lastEntity.polygon.material = Cesium.Color.fromCssColorString(buildDate[lastEntity.id].color).withAlpha(0.09)
      }
      pick.id.polygon.material = Cesium.Color.RED.withAlpha(0.2)
      lastEntity = pick.id
    } else {
      if (lastEntity) {
        //console.log(lastEntity.id)
        lastEntity.polygon.material = Cesium.Color.fromCssColorString(buildDate[lastEntity.id].color).withAlpha(0.09)
      }
      lastEntity = null
    }
  }, Cesium.ScreenSpaceEventType.MOUSE_MOVE)//鼠标移动事件
  handler.setInputAction((event) => {
    let pick = viewer.scene.pick(event.position)//获取鼠标点击的位置
    if (pick && pick.id) {
      if (pick.id.name) {
        data.dialogVisible = true
        const arr = pick.id.id.split('_')
        data.title = arr[0] + '_' + (Number(arr[2])+1)
        // console.log(pick.id.id)
        // data.title = pick.id.name
      } else {
        viewer.entities.removeAll()
        handler.removeInputAction(Cesium.ScreenSpaceEventType.MOUSE_MOVE)
        const view = buildDate[pick.id.id].view
        const positionArr = view.cameraPosition.split(',').map(a => Number(a))
        const ortArr = view.cameraOrt.split(',').map(a => Number(a))
        viewer.camera.flyTo({
          destination: new Cesium.Cartesian3(...positionArr),//飞行到的位置
          orientation: {
            heading: ortArr[0],
            pitch: ortArr[1],
            roll: ortArr[2]
          }

        })
        addLabel(pick.id.id)
      }

    }
  }, Cesium.ScreenSpaceEventType.LEFT_CLICK)
}

const addLabel = (id) => {
  buildDate[id].child.forEach((item, index) => {
    viewer.entities.add({
      id:  buildDate[id].type + '_label_' + index,
      name: buildDate[id].type + '_label_' + index,
      position: Cesium.Cartesian3.fromDegrees(...item.position),
      label: {
        text: item.name,
        font: '20px',
        fillColor: Cesium.Color.WHITE,
        backgroundColor: Cesium.Color.BLACK.withAlpha(0.5),
        showBackground: true,//显示背景
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
</style>