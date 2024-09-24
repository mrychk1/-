<template>
    <div id="cesiumContainer">
  
    </div>
  </template>
  
  <script setup>
  import * as Cesium from 'cesium'
  // console.log(Cesium)
  //组件先挂载到网页上，地图在实例化到组件中
  import { h, onMounted } from 'vue'
  //设置cesium ion的token
  Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI3NGFlY2YzZi1mYWU1LTRmNTEtYTIwYy05N2FjY2M0NDgyOWYiLCJpZCI6MjA2ODkwLCJpYXQiOjE3MTIzODYxOTZ9.ZlcLWXOr3z6gTIkTrYfjdYB6rrW0Wu5mPAW3jE8OacU'
  onMounted(() => {
    /* arcgis的影像图层 */
    const esri = new Cesium.ArcGisMapServerImageryProvider({
      url: "https://services.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer",
      enablePickFeatures: false,
    });
    //二维    要素--->数据源--->图层---->地图
    //三维    实体--->地图

    //是操作地图开始的API
    const viewer = new Cesium.Viewer('cesiumContainer', {})
  
    //二维   {type:'point'} 要素
    //三维   {point:{},polyline:{},polygon:{},billboard:{},label:{}}  组合式实体
  
    //组合式实体
    const entity = viewer.entities.add({
      position: Cesium.Cartesian3.fromDegrees(120, 30, 100),
      billboard: {
        /* 这里使用相对路径可能会出现问题，一般使用绝对路径 */
        image: '/src/assets/position.png',//图片
        scale: 0.3,//缩放比例
        color: Cesium.Color.YELLOW,//颜色
      },
      polyline: {
        positions: Cesium.Cartesian3.fromDegreesArrayHeights([
          120, 30, 100,
          120 , 30, 0
        ]),
        material: new Cesium.PolylineDashMaterialProperty({
          color: Cesium.Color.AQUA,
          dashLength: 16.0,
          dashPattern: 255.0
        }),
      },
      label: {
        text: 'xx小区',//文本内容
        pixelOffset: new Cesium.Cartesian2(0, -50),//偏移量
      }
    })
    console.log(entity)
    viewer.zoomTo(entity)
  
  
  
    // //标注
    // const billboard = viewer.entities.add({
    //   position: Cesium.Cartesian3.fromDegrees(120, 30, 100),
    //   billboard: {
    //     /* 这里使用相对路径可能会出现问题，一般使用绝对路径 */
    //     image: '/src/assets/position.png',//图片
    //     scale: 0.3,//缩放比例
    //     color: Cesium.Color.YELLOW,//颜色
    //   }
    // })
  
    // //line
    // const line = viewer.entities.add({
    //   polyline: {
    //     positions: Cesium.Cartesian3.fromDegreesArrayHeights([
    //       120, 30, 100,
    //       120 , 30, 0
    //     ]),
    //     material: new Cesium.PolylineDashMaterialProperty({
    //       color: Cesium.Color.AQUA,
    //       dashLength: 16.0,
    //       dashPattern: 255.0
    //     }),
    //   }
    // })
  
    // //label
    // const label = viewer.entities.add({
    //   position: Cesium.Cartesian3.fromDegrees(120, 30, 100),
    //   label: {
    //     text: 'xx小区',//文本内容
    //     pixelOffset: new Cesium.Cartesian2(0, -50),//偏移量
    //   }
    // })
    // viewer.zoomTo(viewer.entities)
  
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