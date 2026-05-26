<script setup lang="ts">
import { ref } from 'vue';

  const options = {
  enableHighAccuracy: true,
  timeout: 5000,
  maximumAge: 0,
};

//let positions = ref<GeolocationPosition[]>([]);
let positions = ref<number[][]>([[]]);
let TestPosition: number[][] = [[35.69426506378273, 139.74629470257938], [35.67733248152762, 139.74885703005893], [35.67564601093697, 139.75945600414306],[35.68992391968861, 139.76060600375328]]; // 東京、ロサンゼルス、ロンドンの位置

//positions.value = TestPosition;

function savePosition() {
  navigator.geolocation.getCurrentPosition(
    (pos) => {
      console.log("位置情報を保存しました:", pos);
      const crd = pos.coords;

      console.log("現在の位置:");
      console.log(`緯度: ${crd.latitude}`);
      console.log(`軽度: ${crd.longitude}`);
      console.log(`誤差 ${crd.accuracy} メートル`);
      positions.value.push([crd.latitude, crd.longitude]);
    },
    (err) => {
      console.error("位置情報の取得に失敗しました:", err);
    },
    options
  );
}

function latLngToXY(lat:number, lng:number, baseLat:number, baseLng:number) {
  const latRad = baseLat * Math.PI / 180;

  const x =
    (lng - baseLng) *
    111320 *
    Math.cos(latRad);

  const y =
    (lat - baseLat) *
    111320;

  return { x, y };
}

function calcAreaSize(){
  if (positions.value.length < 3) {
    console.warn("少なくとも3点が必要です。");
    return 0;
  }
  let sum = 0;
  const baseLat = positions.value[0][0];
  const baseLng = positions.value[0][1];
  
  const points = positions.value.map(pos => 
    //latLngToXY(pos.coords.latitude, pos.coords.longitude, baseLat!, baseLng!)
    latLngToXY(pos[0]||0, pos[1]||0, baseLat!, baseLng!)
  );
  
  for (let i = 0; i < points.length; i++) {
    const j = (i + 1) % points.length;

    const pi = points[i];
    const pj = points[j];
    if (pi && pj) {
      sum += pi.x * pj.y;
      sum -= pj.x * pi.y;
    }
  }

  return Math.abs(sum) / 2;

}

//navigator.geolocation.getCurrentPosition(success, error, options);
</script>

<template>
  <h1>You did it!</h1>
  <p>
    Visit <a href="https://vuejs.org/" target="_blank" rel="noopener">vuejs.org</a> to read the
    documentation
  </p>
  <div>
    <h2>保存された位置情報:</h2>
    <ul>
      <li v-for="(pos, index) in positions" :key="index">
        緯度: {{ pos[0] }}, 経度: {{ pos[1] }}
      </li>
    </ul>
  </div>
  <button @click="savePosition">位置情報を保存</button>
  <div>
    <h2>面積:</h2>
    <p>{{ calcAreaSize() }} 平方メートル</p>
  </div>
</template>

<style scoped></style>
