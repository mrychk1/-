<template>
  <div id="cesiumContainer">

  </div>
</template>

<script setup>
import * as Cesium from 'cesium'
// console.log(Cesium)
//组件先挂载到网页上，地图在实例化到组件中
import { onMounted } from 'vue'
//引入turf
import * as turf from '@turf/turf'
//设置cesium ion的token
Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI3NGFlY2YzZi1mYWU1LTRmNTEtYTIwYy05N2FjY2M0NDgyOWYiLCJpZCI6MjA2ODkwLCJpYXQiOjE3MTIzODYxOTZ9.ZlcLWXOr3z6gTIkTrYfjdYB6rrW0Wu5mPAW3jE8OacU'

//在外面申明比较好,不然会报错
let viewer, handler

onMounted(() => {
  //是操作地图开始的API
  viewer = new Cesium.Viewer('cesiumContainer', {
    infoBox: false,//关闭信息框
    selectionIndicator: false,//关闭选中指示器
  })

  //墨尔本倾斜摄影
  const tileset = viewer.scene.primitives.add(
    new Cesium.Cesium3DTileset({
      url: Cesium.IonResource.fromAssetId(75343)
    })
  )
  viewer.zoomTo(tileset)

  //线
  let arr = []

  const line = viewer.entities.add({
    polyline: {
      positions: [],
      width: 10,
      material: Cesium.Color.BLUE,
      clampToGround: true//贴地
    }
  })



  handler = new Cesium.ScreenSpaceEventHandler(viewer.scene.canvas)
  //设置左键点击事件
  handler.setInputAction((event) => {
    console.log(1)
    //拾取坐标
    // let position = viewer.scene.pickPosition(event.position)
    // if (position) {
    //   console.log(position)
    // }
    // 拾取要素
    // let pick = viewer.scene.pick(event.position)
    // console.log(pick)
    // //通过是拾取到的数据来修改颜色
    // if (pick && pick.id) {
    //   pick.id.point.color = Cesium.Color.BLUE
    // }

    // 画线
    let position = viewer.scene.pickPosition(event.position)
    if (position) {
      viewer.entities.add({
        position: position,
        point: {
          pixelSize: 30,
          color: Cesium.Color.RED
        }
      })
      arr.push(position)
      line.polyline.positions = arr
    }
  }, Cesium.ScreenSpaceEventType.LEFT_CLICK)//左键点击事件
  //设置右键点击事件
  handler.setInputAction(() => {
    //只留一条线且结束画笔
    /* 只留一条线 */


    /* 结束画笔 */
    handler.removeInputAction(Cesium.ScreenSpaceEventType.LEFT_CLICK)//结束左键点击事件
    handler.removeInputAction(Cesium.ScreenSpaceEventType.RIGHT_CLICK)//结束右键点击事件
  }, Cesium.ScreenSpaceEventType.RIGHT_CLICK)//右键点击事件
})


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
  top: 10px;
  left: 10px;
  z-index: 999;
}
</style>