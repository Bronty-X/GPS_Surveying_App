<script setup lang="ts">
import { ref } from 'vue';

  const options = {
  enableHighAccuracy: true,
  timeout: 5000,
  maximumAge: 0,
};

let positions = ref<GeolocationPosition[]>([]);

function savePosition() {
  navigator.geolocation.getCurrentPosition(
    (pos) => {
      console.log("位置情報を保存しました:", pos);
      const crd = pos.coords;

      console.log("現在の位置:");
      console.log(`緯度: ${crd.latitude}`);
      console.log(`軽度: ${crd.longitude}`);
      console.log(`誤差 ${crd.accuracy} メートル`);
      positions.value.push(pos);
    },
    (err) => {
      console.error("位置情報の取得に失敗しました:", err);
    },
    options
  );
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
        緯度: {{ pos.coords.latitude }}, 軽度: {{ pos.coords.longitude }}, 誤差: {{ pos.coords.accuracy }} メートル
      </li>
    </ul>
  </div>
  <button @click="savePosition">位置情報を保存</button>
</template>

<style scoped></style>
