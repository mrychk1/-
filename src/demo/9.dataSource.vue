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
  let viewer
  
  
  
  onMounted(() => {
    //是操作地图开始的API
    viewer = new Cesium.Viewer('cesiumContainer', {
      // infoBox: false,//关闭信息框
      // selectionIndicator: false,//关闭选中指示器
      shouldAnimate: true //开启场景动画
    })
  
    //加载geojson数据
    // const linestring = turf.lineString([[-24, 63], [-23, 60], [-25, 65], [-20, 69]]);
    // let promise = Cesium.GeoJsonDataSource.load(linestring);//加载geojson数据
    // promise.then(res=>{//加载成功后的回调
    //   console.log(res)
    //   let entity = res.entities.values[0]//获取实体
    //   viewer.entities.add(entity)
    //   viewer.zoomTo(entity)
    // })
  
    // const polygon = turf.polygon([[[-5, 52], [-4, 56], [-2, 51], [-5, 52]]]);
    // Cesium.GeoJsonDataSource.load(polygon).then(res => {
    //   data = res
    //   viewer.dataSources.add(data)
    //   console.log(viewer.dataSources)
    // })
    // viewer.zoomTo(viewer.dataSources)
  
    // const multiLine = turf.multiLineString([[[0, 0], [4, 4]], [[6, 6], [10, 10]]])
    // const promise = Cesium.GeoJsonDataSource.load(multiLine)//加载geojson数据
    // viewer.dataSources.add(promise)
    // viewer.zoomTo(promise)
  
    //加载topojson数据
    // const promise = Cesium.GeoJsonDataSource.load('/src/assets/usa.topojson')
    // viewer.dataSources.add(promise)
    // viewer.zoomTo(promise)
  
    //加载kml数据
    // const promise = Cesium.KmlDataSource.load('/src/assets/gdp2008.kmz')
    // viewer.dataSources.add(promise)
    // viewer.zoomTo(promise)
  
    //加载动态数据格式CZML
    const data = Cesium.CzmlDataSource.load('/src/assets/vehicle.czml')
    //Cesium.CzmlDataSource
    data.then(res => {
      viewer.dataSources.add(res);
      let entity = res.entities.getById("Vehicle")
      viewer.trackedEntity = entity; //trackedEntity 可以实现⼀直移动相机跟踪entity
    })
    viewer.zoomTo(data)
    
  
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