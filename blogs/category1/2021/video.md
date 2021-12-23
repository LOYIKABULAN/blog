---
title: 调用摄像头
date: 2021-12-23
tags:
  - js
categories:
  - category1
---

first page in category1

<template>
  <div id="app">
    <video id="video" width="400" height="300"></video>
    <canvas id="canvas"></canvas>
    <button id="live">直播</button>
    <button id="snap">截图</button>
  </div>
</template>

<script>
export default {
  name: "App",
  components: {},
  mounted() {
    console.log("hello");
    var video = document.getElementById("video");
    var canvas = document.getElementById("canvas");
    var ctx = canvas.getContext("2d");
    var width = video.width;
    var height = video.height;
    canvas.width = width;
    canvas.height = height;
    function liveVideo() {
      // var URL = window.URL || window.webkitURL; // 获取到window.URL对象
      navigator.getUserMedia(
        {
          video: true,
        },
        function (stream) {
          video.srcObject = stream; // 将获取到的视频流对象转换为地址
          video.play(); // 播放
          //点击截图
          document
            .getElementById("snap")
            .addEventListener("click", function () {
              ctx.drawImage(video, 0, 0, width, height);
              var url = canvas.toDataURL("image/png");
              document.getElementById("download").href = url;
            });
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
</style>
