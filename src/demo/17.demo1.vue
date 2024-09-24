<template>
  <div id="cesiumContainer">

  </div>
  <el-card class="box-card">
    <template #header>
      <div class="card-header">
        <span>小区设施</span>
      </div>
    </template>
    <div v-for="(item, index) in data.arr" @click="toFly(item)" :key="index" class="text">{{ item.name }}</div>
  </el-card>
</template>

<script setup>
import * as Cesium from 'cesium'
// console.log(Cesium)
//组件先挂载到网页上，地图在实例化到组件中
import { onMounted, reactive } from 'vue'
//引入turf
import * as turf from '@turf/turf'
//引入tools中的load中的方法
import { load3dtiles, update3dtiles } from './tools/load'
//引入数据
import facilities from '/src/assets/facilities.json'

//设置cesium ion的token
Cesium.Ion.defaultAccessToken = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJqdGkiOiI3NGFlY2YzZi1mYWU1LTRmNTEtYTIwYy05N2FjY2M0NDgyOWYiLCJpZCI6MjA2ODkwLCJpYXQiOjE3MTIzODYxOTZ9.ZlcLWXOr3z6gTIkTrYfjdYB6rrW0Wu5mPAW3jE8OacU'

//在外面申明比较好,不然会报错
let viewer

//初始化数据
const data = reactive({
  arr: []
})

const initData = () => {
  data.arr = facilities
  // console.log(data.arr)
}
const toFly = (item) => {
  const positionArr = item.cameraPosition.split(',').map(a => Number(a))
  const oriArr = item.cameraOrt.split(',').map(a => Number(a))
  viewer.camera.flyTo({
    destination: new Cesium.Cartesian3(...positionArr),
    orientation:{
      heading: oriArr[0],
      pitch: oriArr[1],
      roll: oriArr[2]
    }
  })
}

onMounted(() => {
  //是操作地图开始的API
  viewer = new Cesium.Viewer('cesiumContainer', {
    infoBox: false,//关闭信息框
    selectionIndicator: false,//关闭选中指示器
  })

  //加载3dtiles
  load3dtiles(viewer, '/src/assets/b3dm/tileset.json', (tileset) => {
    update3dtiles(tileset)
    viewer.zoomTo(tileset)
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



.box-card {
  width: 300px;
  margin: 10px;
  border-radius: 4px;
  box-shadow: 0 2px 12px 0 rgba(0, 0, 0, 0.1);
  position: absolute;
  top: 10px;
  left: 10px;
}
</style>