<template>
    <div id="cesiumContainer">
  
    </div>
    <button class="btn" @click="toDel">删除</button>
  </template>
  
  <script setup>
  import * as Cesium from 'cesium'
  // console.log(Cesium)
  //组件先挂载到网页上，地图在实例化到组件中
  import { h, onMounted } from 'vue'
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
  
    //二维    要素--->数据源--->图层---->地图
    //三维    实体--->地图
  
    //二维    100蓝点---->数据源    100红点---->数据源
    //三维    将一个自定义的空数组当作数据源来用
    point1 = viewer.entities.add({
      
      position: Cesium.Cartesian3.fromDegrees(120, 30),//笛卡尔坐标
      point: {
        pixelSize: 20,
        color: Cesium.Color.BLUE
      }
    })
    blueList.push(point1)
    point3 = viewer.entities.add({
      
      position: Cesium.Cartesian3.fromDegrees(119.5, 30),//笛卡尔坐标
      point: {
        pixelSize: 20,
        color: Cesium.Color.BLUE
      }
    })
    blueList.push(point3)
    point2 = viewer.entities.add({
      
      position: Cesium.Cartesian3.fromDegrees(120.5, 30),//笛卡尔坐标
      point: {
        pixelSize: 20,
        color: Cesium.Color.RED
      }
    })
    viewer.zoomTo(viewer.entities)
  })
  const toDel = () => {
    // // 1.直接删除实体
    // viewer.entities.remove(point1)
    // //2.删除所有实体
    // viewer.entities.removeAll()
    // //3.通过ID删除实体
    // viewer.entities.removeById('point2')
    // //4.先查后删
    // const entity = viewer.entities.getById('point2')
    // viewer.entities.remove(entity)
    // 5.分类删除
    blueList.forEach(item => {
      viewer.entities.remove(item)
    })
    /* 清空数组 */
    blueList = [] //不要忘了清空数组
    console.log(viewer.entities)
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
    /* //绝对定位 */
    top: 10px;
    left: 10px;
    width: 100px;
    height: 40px;
    z-index: 999;
  }
  </style>