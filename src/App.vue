<template>
  <div id="app">
    <button @click="startCamera()" id="start-camera">Start Camera</button>
    <video id="video" rewidth="320" height="240" autoplay></video>

    <button @click="clickPhoto()" ref="start-camera" id="click-photo">Click Photo</button>
    <canvas id="canvas" width="320" height="240"></canvas>




    <button v-on:click="recognize">recognize</button>
    <img id="text-img" alt="Vue logo" src="./assets/testocr.png">
  </div>
</template>

<script lang="ts">
/* eslint-disable */
import { createWorker, PSM, OEM } from 'tesseract.js';
const worker = createWorker({
  logger: m => console.log(m),
});
export default {
  name: 'app',

  methods: {

    startCamera: async () => {
      const video = <HTMLVideoElement> document.getElementById('video');

      let stream = await navigator.mediaDevices.getUserMedia({ video: {
          facingMode: 'environment'
      } })

      if (video != null) video.srcObject = stream 

	    
    },

    clickPhoto: async () => {
      const canvas = <HTMLCanvasElement> document.getElementById('canvas')
      const video = <HTMLVideoElement> document.getElementById('video')
      const ctx = canvas.getContext('2d')
 
      if (canvas != null && video != null && ctx != null) {
        ctx.drawImage(video, 0, 0, canvas.width, canvas.height)
        let image_data_url = canvas.toDataURL('image/jpeg')

        console.log(image_data_url);
      }
    },

    recognize: async () => {
      const img = document.getElementById('text-img');
      console.log(img);
      await (await worker).loadLanguage('eng');
      await (await worker).initialize('eng', OEM.LSTM_ONLY);
      await (await worker).setParameters({
        tessedit_pageseg_mode: PSM.SINGLE_BLOCK,
      });
      const { data: { text } } = await (await worker).recognize(img);
      console.log(text);
    }
  }
}
</script>

<style>
#app {
  font-family: 'Avenir', Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
</style>