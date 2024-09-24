
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
    //笛卡尔坐标(Cesium里面绝大多数都用笛卡尔坐标)
    const position = new Cesium.Cartesian3(100, 200, 300)//z轴不是高度
    //经纬度转笛卡尔坐标(110,20)
    const cartesian1 = Cesium.Cartesian3.fromDegrees(110, 20, 100)
    const cartesian2 = Cesium.Cartesian3.fromDegrees(110, 20, 200)//经度 纬度  高度  (默认高度是0  高度为地球表面到点的高度改变高度笛卡尔坐标中的xyz三个轴都会变)
    console.log(cartesian1)
    console.log(cartesian2)
  
    //笛卡尔转经纬度(两个步骤)
    //1.笛卡尔转弧度坐标(包含高度)
    const cartographic = Cesium.Cartographic.fromCartesian(cartesian1)
    //2.弧度坐标转角度坐标
    let lon = Cesium.Math.toDegrees(cartographic.longitude)
    let lat = Cesium.Math.toDegrees(cartographic.latitude)
    // let lon = 180/Math.PI*cartographic.longitude
    // let lat = 180/Math.PI*cartographic.latitude
    
    console.log(lon,lat)
    // console.log(cartographic) 
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