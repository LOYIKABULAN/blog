---
title: 调用摄像头我的兼容
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
      <video id="video" style="width: 50%; height: auto" loop="true"  preload="none" webkit-playsinline="" playsinline="true" controls></video>
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
     const script= document.createElement('script');
     script.type = 'text/javascript';
     script.src = `https://cdn.bootcss.com/vConsole/3.2.0/vconsole.min.js`;
     document.body.appendChild(script);
 
     setTimeout(()=> {
				let vConsole = new VConsole();
			}, 1000);
    
    console.log('测试');

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
        if(navigator.getUserMedia){
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
        }else{
         if (navigator.mediaDevices === undefined) {
          navigator.mediaDevices = {};
        }
        // 一些浏览器部分支持 mediaDevices。我们不能直接给对象设置 getUserMedia
        // 因为这样可能会覆盖已有的属性。这里我们只会在没有getUserMedia属性的时候添加它。
        if (navigator.mediaDevices.getUserMedia === undefined) {
          navigator.mediaDevices.getUserMedia = function (constraints) {
            // 首先，如果有getUserMedia的话，就获得它
            var getUserMedia =
              navigator.webkitGetUserMedia || navigator.mozGetUserMedia;

            // 一些浏览器根本没实现它 - 那么就返回一个error到promise的reject来保持一个统一的接口
            if (!getUserMedia) {
              return Promise.reject(
                new Error("getUserMedia is not implemented in this browser")
              );
            }

            // 否则，为老的navigator.getUserMedia方法包裹一个Promise
            return new Promise(function (resolve, reject) {
              getUserMedia.call(navigator, constraints, resolve, reject);
            });
          };
        }

        navigator.mediaDevices
          .getUserMedia({ audio: true, video: true })
          .then(function (stream) {
            var video = document.querySelector("video");
            // 旧的浏览器可能没有srcObject
            if ("srcObject" in video) {
              video.srcObject = stream;
              video.play();
              document
                .getElementById("snap")
                .addEventListener("click", function () {
                  ctx.drawImage(video, 0, 0, width, height);
                  var url = canvas.toDataURL("image/png");
                  document.getElementById("download").href = url;
                  document.getElementById("download").download = url;
                });
            } else {
              // 防止在新的浏览器里使用它，应为它已经不再支持了
              video.src = window.URL.createObjectURL(stream);
              video.play();
              document
                .getElementById("snap")
                .addEventListener("click", function () {
                  ctx.drawImage(video, 0, 0, width, height);
                  var url = canvas.toDataURL("image/png");
                  document.getElementById("download").href = url;
                  document.getElementById("download").download = url;
                });
            }
            video.onloadedmetadata = function (e) {
              video.play();
              
            };
          })
          .catch(function (err) {
            console.log(err.name + ": " + err.message);
          });
        }
        
      
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
