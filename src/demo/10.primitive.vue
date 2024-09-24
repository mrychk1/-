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
      infoBox: false,//关闭信息框
      selectionIndicator: false,//关闭选中指示器
    })
  
    // entity
    // 调⽤⽅便，封装完美
    // 是基于primitive的封装
  
    // primitive
    // 更接近底层
    // 可以绘制⾼级图形、组合图形
    // 由Geometry(⼏何形状)、(Appearance )外观组成
  
    /* 多数数据渲染使用primitive */
  
    // // 1. Draw a translucent ellipse on the surface with a checkerboard pattern
    // const promitive = new Cesium.Primitive({
    //   /* 决定图片形状 */
    //   geometryInstances: new Cesium.GeometryInstance({
    //     geometry: new Cesium.EllipseGeometry({
    //       center: Cesium.Cartesian3.fromDegrees(-100.0, 20.0),
    //       semiMinorAxis: 500000.0,
    //       semiMajorAxis: 1000000.0,
    //       rotation: Cesium.Math.PI_OVER_FOUR,
    //       vertexFormat: Cesium.VertexFormat.POSITION_AND_ST
    //     }),
    //   }),
    //   /* 决定图片的外观 */
    //   appearance: new Cesium.EllipsoidSurfaceAppearance({
    //     material: Cesium.Material.fromType('Checkerboard')
    //   })
    // })
    // viewer.scene.primitives.add(promitive);
  
    // 2. Draw different instances each with a unique color
    /* 矩形 */
    const rectangleInstance = new Cesium.GeometryInstance({
      geometry: new Cesium.RectangleGeometry({
        rectangle: Cesium.Rectangle.fromDegrees(-140.0, 30.0, -100.0, 40.0),
        vertexFormat: Cesium.PerInstanceColorAppearance.VERTEX_FORMAT
      }),
      id: 'rectangle',
      attributes: {
        color: new Cesium.ColorGeometryInstanceAttribute(0.0, 1.0, 1.0, 0.5)
      }
    });
    /* 椭球 */
    const ellipsoidInstance = new Cesium.GeometryInstance({
      geometry: new Cesium.EllipsoidGeometry({
        radii: new Cesium.Cartesian3(500000.0, 500000.0, 1000000.0),
        vertexFormat: Cesium.VertexFormat.POSITION_AND_NORMAL
      }),
      modelMatrix: Cesium.Matrix4.multiplyByTranslation(Cesium.Transforms.eastNorthUpToFixedFrame(
        Cesium.Cartesian3.fromDegrees(-95.59777, 40.03883)), new Cesium.Cartesian3(0.0, 0.0, 500000.0), new Cesium.Matrix4()),
      attributes: {
        color: Cesium.ColorGeometryInstanceAttribute.fromColor(Cesium.Color.AQUA)
      }
    });
    /* 多边形（三角形） */
    const polygonInstance = new Cesium.GeometryInstance({
      geometry: new Cesium.PolygonGeometry({
        polygonHierarchy: new Cesium.PolygonHierarchy(
          Cesium.Cartesian3.fromDegreesArray([
            -100, 42,
            -100, 28,
            -90, 35,
          ])
        )
      }),
      attributes: {
        color: Cesium.ColorGeometryInstanceAttribute.fromColor(Cesium.Color.AQUA)
      }
    });
  
  
    const primitive = new Cesium.Primitive({
      geometryInstances: [rectangleInstance, polygonInstance],
      appearance: new Cesium.PerInstanceColorAppearance()
    })
    viewer.scene.primitives.add(primitive);
  
  
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