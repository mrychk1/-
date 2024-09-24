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
  onMounted(() => {
    /* arcgis的影像图层 */
    const esri = new Cesium.ArcGisMapServerImageryProvider({
      url: "https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer",
      enablePickFeatures: false,
    });
    //是操作地图开始的API
    const viewer = new Cesium.Viewer('cesiumContainer', {})
    const position = Cesium.Cartesian3.fromDegrees(110,20,20000)
    //setView通过定义相机的目的地，直接跳转到目的地
    // viewer.camera.setView({
    //     destination:position,//使用的是笛卡尔坐标
    //     orientation:{//默认(0,90,0)
    //       heading:Cesium.Math.toRadians(0),//方向
    //       pitch:Cesium.Math.toRadians(-90),//俯仰角
    //       roll:Cesium.Math.toRadians(40)//翻滚角
    //     }
    // })
    
    // //flyTo带飞行动画，可以设置飞行时长
    // viewer.camera.flyTo({
    //   destination:position,//使用的是笛卡尔坐标
    //   duration:3//设置飞行时长(秒)
    // })
  
  
    const position1= Cesium.Cartesian3.fromDegrees(110,20)
    //lookAt将视角固定在设置的点位上  可以选择视角  不能改变位置
    viewer.camera.lookAt(
      position1,//目标点
      new Cesium.HeadingPitchRange(
        /* toRadians是一个函数，用于将角度转换为弧度 */
        Cesium.Math.toRadians(0),//方向
        Cesium.Math.toRadians(-90),//俯仰角
        20000//距离
      )
    )
  
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