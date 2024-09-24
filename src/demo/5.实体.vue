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
    //是操作地图开始的API
    const viewer = new Cesium.Viewer('cesiumContainer', {
      infoBox: false,//关闭信息框
      selectionIndicator: false,//关闭选中指示器
    })
    //二维    要素--->数据源--->图层---->地图
    //三维    实体--->地图
    // 写法一：
    // const point = new Cesium.Entity({
    //   position: Cesium.Cartesian3.fromDegrees(120,30),
    //   point:{
    //     pixelSize:30,
    //     color:Cesium.Color.RED
    //   }
    // })
    // viewer.zoomTo(point)
    // viewer.entities.add(point)
  
    // // 写法二：
    //  point
    // const point = viewer.entities.add({
    //   id:'point',//id必要且唯一
    //   position: Cesium.Cartesian3.fromDegrees(120,30),//笛卡尔坐标
    //   point:{
    //     pixelSize:30,
    //     color:Cesium.Color.RED
    //   }
    // })
    // viewer.zoomTo(point)
    // console.log(point)//实体
    // console.log(viewer.entities)//数据源
    // // viewer.entities.add(point)
  
    // //文本
    // const label = viewer.entities.add({
    //   position: Cesium.Cartesian3.fromDegrees(112, 30),
    //   label: {
    //     text: 'Hello Cesium',//文本内容
    //     fillColor: Cesium.Color.RED,//填充颜色
    //     showBackground: true,//显示背景
    //     backgroundColor: Cesium.Color.YELLOW,//背景颜色
    //     backgroundPadding: new Cesium.Cartesian2(50, 20),//背景内边距
    //   }
    // })
    // viewer.zoomTo(label)
  
    //标注
    const billboard = viewer.entities.add({
      position: Cesium.Cartesian3.fromDegrees(112, 30, 30),
      billboard: {
        /* 这里使用相对路径可能会出现问题，一般使用绝对路径 */
        image: '/src/assets/position.png',//图片
        scale:0.3,//缩放比例
        color:Cesium.Color.YELLOW,//颜色
      }
    })
  
    //线
    const line = viewer.entities.add({
      polyline: {
        positions: Cesium.Cartesian3.fromDegreesArray([
          110, 30,
          120, 30,
          120, 40,
        ]),//线的坐标 返回的是一个笛卡尔坐标数组
        width: 5,//线的宽度
        material: Cesium.Color.RED,//线的颜色
      }
    })
    // viewer.zoomTo(line)
  
    //多边形
    const polygon = viewer.entities.add({
      polygon: {
        hierarchy: {
          positions: Cesium.Cartesian3.fromDegreesArray([
            110, 30,
            120, 30,
            120, 40,
            110, 40,
          ]),//多边形的坐标 返回的是一个笛卡尔坐标数组
        },
        material: Cesium.Color.fromRandom({ alpha: 0.5 }),//多边形的颜色 随机颜色
        height: 300000,//高度   多边形距离地球表面的高度
        extrudedHeight: 400000,//拉伸高度   多边形的突出面相对于地球表面的高度
        outline: true,//是否显示轮廓
        outlineColor: Cesium.Color.fromCssColorString('#fff'),//颜色字符串
        fill: false,//是否填充
      }
    })
    // viewer.zoomTo(polygon)
  
    //立方体
    const box = viewer.entities.add({
      position: Cesium.Cartesian3.fromDegrees(119, 30, 3000),
      box: {
        dimensions: new Cesium.Cartesian3(2000, 1000, 3000),//立方体的长宽高
        material: Cesium.Color.BLUE,//立方体的颜色
      }
    })
    // viewer.zoomTo(box)
  
    //椭圆
    const ellipse = viewer.entities.add({
      position: Cesium.Cartesian3.fromDegrees(118, 30),//椭圆的中心点
      ellipse: {
        semiMinorAxis: 100000,//短半轴
        semiMajorAxis: 200000,//长半轴
        material: Cesium.Color.GREEN,//颜色
        outline: true,//是否显示轮廓
        outlineColor: Cesium.Color.RED,//轮廓颜色
        rotation: Cesium.Math.toRadians(45),//旋转角度
      }
    })
    // viewer.zoomTo(ellipse)
  
    //矩形
    const rectangle = viewer.entities.add({
      rectangle: {
        coordinates: Cesium.Rectangle.fromDegrees(120, 40, 123, 45),//矩形的坐标
        material: '/src/assets/position.png',//可以用自定义的图片填充
        extrudedHeight: 300000,//拉伸高度
      }
    })
    // viewer.zoomTo(rectangle)
  
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