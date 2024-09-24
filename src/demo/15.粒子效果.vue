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
      /* 开始动画 */
      shouldAnimate: true,
      /* 开启阴影 */
      shadows: true,
    })
    const position = Cesium.Cartesian3.fromDegrees(114, 30, 300)
    const orientation = Cesium.Transforms.headingPitchRollQuaternion(
      position,
      Cesium.HeadingPitchRoll.fromDegrees(0, 0, 0)
    )
    const model = viewer.entities.add({
      position,
      orientation,
      model: {
        uri: '/src/assets/Cesium_Air.glb',
      }
    })
    viewer.zoomTo(model)
  
    viewer.scene.primitives.add(new Cesium.ParticleSystem({
      image: '/src/assets/fire.png',
      imageSize: new Cesium.Cartesian2(20, 20),//粒子大小
      startScale: 1,//开始大小
      endScale: 4,//结束大小
      particleLife: 3,//粒子生命周期
      speed: 5,//发射粒子速度
      // emitter: new Cesium.CircleEmitter(2),//设置发射器(圆形发射器)
      emitter: new Cesium.BoxEmitter(new Cesium.Cartesian3(10, 10, 10)),
      emissionRate: 5,//每秒发射粒子数
      modelMatrix: model.computeModelMatrix(
        viewer.clock.startTime,//时间控件中的起始时间
        new Cesium.Matrix4()//4*4矩阵数据
      ),//设置位置
      lifetime: 16,//粒子系统生命周期
  
    })
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
  
  .btn {
    position: absolute;
    top: 10px;
    left: 10px;
    z-index: 999;
  }
  </style>