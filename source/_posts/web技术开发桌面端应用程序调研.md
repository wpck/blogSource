---
title: web技术开发桌面端应用程序调研
date: 2020-12-16 13:31:12
tags:
cover: ./img/12client.jpg
---
### 场景
nwjs和elecron是一个使用web技术来开发桌面应用的框架; 适用于使用web技术开发跨平台桌面应用;   
特点: 缺少共享, 不需要浏览器运行, 以window窗口形式存在; 每台计算机上都需要先安装;
web的优点: 资源共享:一个url,所有人都可以访问; 部署方便,只需要服务器上部署一次;
### nwjs API
nwjs提供的API包括一下几个方面:   
>+ 配置文件: 配置应用程序的入口文件,名称,窗体样式,icon等;
>+ 

### nw.js与electron对比
+ nw.js与electron相似,都是基于chromium和nodejs实现的,提供了一个能通过JS,HTML来创建桌面应用的平台;同时集成node来授权网页来访问底层系统的权限; 为了弥补nodejs和js访问系统API方面的不足,两个框架都对系统API进行了封装,比如系统对话框,系统托盘,系统菜单,剪贴板等,开发者基于electron开发时,可以直接使用js访问这些API, 其他本地文件的访问,网络控制等可以由nodejs提供支持;
+ 最主要的区别是electron区分主进程和渲染进程
>electron主进程负责创建和管理渲染进程以及整个应用的生命周期,渲染进程负责显示界面以及控制用户的交互逻辑;主进程和渲染进程间的通信都要通过ipcMain和ipcRenderer实现;    
>nwjs无需关注这些问题,需要关注的是所有窗口共享一个node环境带来的问题;
+ chromium和nodejs的应用场景完全不同,nwjs和electron需要做的是把他们整合起来:
>nwjs通过整个源码合并了nodejs和chromiun的事件循环机制,但是会使nodejs和chromium的耦合性更高;
>electron则是通过各操作系统的消息循环打通了nodejs和chromium的事件循环机制;

### 优化对比
关于框架打包的应用程序和web程序关于性能上的对比,未找到相关的文档;   
因为本质上都是在浏览器里运行的同一套代码,故性能上差别不大;    

