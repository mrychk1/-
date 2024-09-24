<template>
    <div id="cesiumContainer">
  
    </div>
  </template>
  
  <script setup>
  import * as Cesium from 'cesium'
  console.log(Cesium)
  //组件先挂载到网页上，地图在实例化到组件中
  import { onMounted } from 'vue'
  //设置cesium ion的token
  Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI3NGFlY2YzZi1mYWU1LTRmNTEtYTIwYy05N2FjY2M0NDgyOWYiLCJpZCI6MjA2ODkwLCJpYXQiOjE3MTIzODYxOTZ9.ZlcLWXOr3z6gTIkTrYfjdYB6rrW0Wu5mPAW3jE8OacU'
  onMounted(() => {
    /* arcgis的影像图层 */
    const esri = new Cesium.ArcGisMapServerImageryProvider({
      url: "https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer",
      enablePickFeatures: false,
    });
    //是操作地图开始的API
    const viewer = new Cesium.Viewer('cesiumContainer', {
      /* 隐藏动画控件 */
      animation: false,
      /* 隐藏时间轴 */
      timeline: false,
      /* 隐藏全屏按钮 */
      fullscreenButton: false,
      /* 隐藏搜索按钮 */
      geocoder: false,
      /* 隐藏主页按钮 */
      homeButton: false,
      /* 隐藏投影方式 */
      sceneModePicker: false,
      /* 隐藏图层选择 */
      baseLayerPicker: false,
      /* 隐藏帮助按钮 */
      navigationHelpButton: false
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
  </style>