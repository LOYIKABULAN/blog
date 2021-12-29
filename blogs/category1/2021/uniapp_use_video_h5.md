---
title: 在uni-app中使用h5标签
date: 2021-12-29
tags:
  - js
categories:
  - uni-app_h5
---

>在uni-app中使用h5标签可以考虑采用v-html方法  
>并且可以通过document.getElementById获取dom节点  
    

    <view v-html="htmlcontent" id="face"></view>//template

    //script

    data() {
		return {
			htmlcontent: `
			<video id="video" style="width: 80%; height: auto" loop="true"  preload="none" webkit-playsinline="" playsinline="true" >

			</video>
			<canvas id="myCanvas" width="270" height="135" style="border:1px solid #d3d3d3; position:absolute;">
			您的浏览器不支持 HTML5 canvas 标签。
			</canvas>
			`
		};