---
title: 调用摄像头旧版
date: 2021-12-23
tags:
  - js
categories:
  - category1
---

first page in category1

<template>
  <div id="app">
    <div class="show">
      <video id="video" style="width: 50%; height: auto"></video>
      <video id="video2" style="width: 50%; height: auto"></video>
      <canvas id="canvas"></canvas>
    </div>
    <div class="button">
      <button id="live">直播</button>
      <button id="snap">截图</button>
      <button id="record">录制</button>
      <button id="stop">停止录制返回blob</button>
      <a id="download">下载</a>
    </div>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  mounted() {
    console.log("hello");

    var video = document.getElementById("video");
    var video2 = document.getElementById("video2");
    var canvas = document.getElementById("canvas");
    var record = document.getElementById("record");
    var stop = document.getElementById("stop");
    var ctx = canvas.getContext("2d");
    var width = 163;
    var height = 122;
    var chunks = [];
    canvas.width = width;
    canvas.height = height;
    function liveVideo() {
      // var URL = window.URL || window.webkitURL; // 获取到window.URL对象

      // 想要获取一个最接近 1280x720 的相机分辨率
      
        navigator.getUserMedia(
          {
            video: true,
          },
          function (stream) {
            video.srcObject = stream; // 将获取到的视频流对象转换为地址
            console.log("stream", stream);
            video.play(); // 播放
            //点击截图
            document
              .getElementById("snap")
              .addEventListener("click", function () {
                ctx.drawImage(video, 0, 0, width, height);
                var url = canvas.toDataURL("image/png");
                //   document.getElementById("download").href = url;
              });
            var mediaRecorder = new MediaRecorder(stream);

            record.onclick = function () {
              mediaRecorder.start();
              console.log(mediaRecorder.state);
              console.log("recorder started");
              record.style.background = "red";
              record.style.color = "black";
            };
            stop.onclick = function () {
              mediaRecorder.stop();
              console.log(mediaRecorder.state);
              console.log("recorder stopped");
              record.style.background = "";
              record.style.color = "";
            };
            mediaRecorder.onstop = function (e) {
              console.log("data available after MediaRecorder.stop() called.");

              video2.controls = true;
              var blob = new Blob(chunks, { type: "audio/ogg; codecs=opus" });
              chunks = [];
              var audioURL = window.URL.createObjectURL(blob);
              video2.src = audioURL;
              video2.play();
              console.log("recorder stopped", audioURL);
            };
            mediaRecorder.ondataavailable = function (e) {
              console.log("ondataavailable", e);
              chunks.push(e.data);
            };
          },
          function (error) {
            console.log(error.name || error);
          }
        );
      
    }

    document.getElementById("live").addEventListener("click", function () {
      liveVideo();
    });
  },
};
</script>

<style>
#app {
  font-family: Avenir, Helvetica, Arial, sans-serif;
  -webkit-font-smoothing: antialiased;
  -moz-osx-font-smoothing: grayscale;
  text-align: center;
  color: #2c3e50;
  margin-top: 60px;
}
.show {
  height: 100%;
  display: flex;
  flex-wrap: wrap;
  justify-content: space-around;
}
#video,
#canvas {
  transform: rotateY(180deg);
}
</style>
