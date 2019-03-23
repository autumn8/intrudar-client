<template>
  <div id="app">
    <img id="stream" ref="streamimage">
    <canvas ref="tensorcanvas" width="640px" height="380px"></canvas>
  </div>
</template>
<script>
import io from "socket.io-client";
import * as cocoSsd from "@tensorflow-models/coco-ssd";
import "@tensorflow/tfjs";

const socket = io("http://192.168.8.201:3000/");

export default {
  mounted() {
    let currentImage = undefined;
    const streamImage = this.$refs.streamimage;
    const tensorCanvas = this.$refs.tensorcanvas;
    const tensorContext = tensorCanvas.getContext("2d");
    let currentModel = undefined;

    socket.on("image", image => {      
      currentImage = image.data;
      streamImage.src = 'data:image/jpeg;base64,' + image.data;
    });

    cocoSsd.load().then(model => {
      console.log("model obtained");
      currentModel = model;
      detectFrame();
    });

    function detectFrame() {
       currentModel.detect(streamImage).then(predictions => {
        displayPredictions(predictions);
        requestAnimationFrame(detectFrame);
      });
    }

    function displayPredictions(predictions) {   
      tensorContext.drawImage(streamImage, 0, 0);   
      const font = "16px sans-serif";
      tensorContext.font = font;
      tensorContext.textBaseline = "top";

      predictions.forEach(prediction => {
        if (prediction.class != 'person')return;
        const x = prediction.bbox[0];
        const y = prediction.bbox[1];
        const width = prediction.bbox[2];
        const height = prediction.bbox[3];
        // Draw the bounding box.
        tensorContext.strokeStyle = "#00FFFF";
        tensorContext.lineWidth = 4;
        tensorContext.strokeRect(x, y, width, height);
        // Draw the label background.
        tensorContext.fillStyle = "#00FFFF";
        const textWidth = tensorContext.measureText(prediction.class).width;
        const textHeight = parseInt(font, 10); // base 10
        tensorContext.fillRect(x, y, textWidth + 4, textHeight + 4);
        tensorContext.fillStyle = "#000000";
        tensorContext.fillText(prediction.class, x, y);
      });
    }
  }
};
</script>


<style lang="scss">
#app {
  font-family: "Avenir", Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
}
#nav {
  padding: 30px;
  a {
    font-weight: bold;
    color: #2c3e50;
    &.router-link-exact-active {
      color: #42b983;
    }
  }
}
</style>
