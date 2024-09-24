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
  
  const pinBuilder = new Cesium.PinBuilder();
  const pin300 = pinBuilder
    .fromText("300+", Cesium.Color.RED, 48)
    .toDataURL();
  const pin100 = pinBuilder
    .fromText("100+", Cesium.Color.ORANGE, 48)
    .toDataURL();
  const pin50 = pinBuilder
    .fromText("50+", Cesium.Color.YELLOW, 48)
    .toDataURL();
  const pin20 = pinBuilder
    .fromText("20+", Cesium.Color.GREEN, 48)
    .toDataURL();
  const pin10 = pinBuilder
    .fromText("10+", Cesium.Color.BLUE, 48)
    .toDataURL();
  //在外面申明比较好,不然会报错
  let viewer
  
  onMounted(() => {
    //是操作地图开始的API
    viewer = new Cesium.Viewer('cesiumContainer', {
      infoBox: false,//关闭信息框
      selectionIndicator: false,//关闭选中指示器
    })
  
    Cesium.GeoJsonDataSource.load('/src/assets/camera.json').then(res => {
  
      //开启聚合
      res.clustering.enabled = true
      //设置聚合半径
      res.clustering.pixelRange = 30
      //设置最小聚合数量
      res.clustering.minimumClusterSize = 3
      //设置聚合样式
      res.clustering.clusterEvent.addEventListener((clusteredEntities, cluster) => {
        cluster.billboard.show = true//显示聚合图标
        cluster.label.show = false//隐藏聚合标签
        if (clusteredEntities.length >= 300) {
          cluster.billboard.image = pin300
        } else if (clusteredEntities >= 100) {
          cluster.billboard.image = pin100
        } else if (clusteredEntities >= 50) {
          cluster.billboard.image = pin50
        } else if (clusteredEntities >= 20) {
          cluster.billboard.image = pin20
        } else if (clusteredEntities >= 10) {
          cluster.billboard.image = pin10
        }
      })
  
  
      viewer.dataSources.add(res)
      res.entities.values.forEach(item => {
        item.billboard.image = '/src/assets/camera.png',
          item.billboard.scale = 0.15
      })
      viewer.flyTo(res)
    })
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