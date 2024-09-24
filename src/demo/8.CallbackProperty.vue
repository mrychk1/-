<template>
    <div id="cesiumContainer">
  
    </div>
  </template>
  
  <script setup>
  import * as Cesium from 'cesium'
  // console.log(Cesium)
  //组件先挂载到网页上，地图在实例化到组件中
  import { onMounted } from 'vue'
  //设置cesium ion的token
  Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI3NGFlY2YzZi1mYWU1LTRmNTEtYTIwYy05N2FjY2M0NDgyOWYiLCJpZCI6MjA2ODkwLCJpYXQiOjE3MTIzODYxOTZ9.ZlcLWXOr3z6gTIkTrYfjdYB6rrW0Wu5mPAW3jE8OacU'
  
  //在外面申明比较好,不然会报错
  let viewer, point1, point2, point3
  let blueList = []
  
  onMounted(() => {
    //是操作地图开始的API
    viewer = new Cesium.Viewer('cesiumContainer', {
      infoBox: false,//关闭信息框
      selectionIndicator: false,//关闭选中指示器
    })
    let lon, lat, num = 0
    const line = viewer.entities.add({
      polyline: {
        //CallbackProperty使用时候需要注意跳出循环，不然会非常影响性能
        positions: new Cesium.CallbackProperty(() => {
          console.log(1)
          num += 0.001
          lon = 120 + num//经度
          lat = 30 + num//纬度
          //在121，31的时候停止
          if (lon < 121) {
            return Cesium.Cartesian3.fromDegreesArray([120, 30, lon, lat])
          }else{
            //给positinons赋值一个新的对象  不在需要callbackProperty提供了
            line.polyline.positions = Cesium.Cartesian3.fromDegreesArray([120, 30, 121, 31])
          }
  
        }, false),//不传false会报错
        width: 5,
        material: Cesium.Color.YELLOW
      }
    })
    viewer.zoomTo(line)
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
  </style>