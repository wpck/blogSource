---
title: react底层的一些东西
date: 2020-12-03 20:54:51
tags:
cover: ./img/9react.jpg
---
### react简介
+ 虚拟DOM
>react把真实DOM树转化成js对象树,也就是虚拟DOM(VDOM),每次数据更新后,重新计算VDOM和上一次的VDOM做对比,对变化的部分做更新.VDOM比传统DOM操作好一些,但并不明显,对比DOM节点也需要计算,也是耗性能的.react最大的好处是方便的和其他平台集成,因为react组件可以映射为对应的原生控件,在输出的时候,控制输出web DOM,android或ios控件;
+ 函数式编程是react的精髓
### react设计理念
内存中维护一棵虚拟DOM(VDOM)树,数据变化时,生成一棵新的VDOM树,diff算法比对新老DOM树,找到有变化的部分,得到Change(Patch补丁),将Patch加入队列,最终批量更新Patch到DOM中;
### react fiber 架构
页面是一帧一帧渲染出来的,当每秒钟绘制的帧数达到60帧时,页面是流畅的,故如果每帧的渲染时间超过16ms时,用户会感到卡顿. 浏览器是单线程,js计算,页面布局,页面绘制都是在浏览器主线程完成的;   
react16引入了fiber架构,再此之前react存在以下问题:   
>当调用setState时,react会遍历所有节点,计算出差异,然后更新UI,整个过程是不能被打断的,如果页面元素很多,整个过程可能会大于16ms而出现掉帧现象;   
>旧版react采用递归的方式自顶向下遍历生成新的VDOM渲染,使用js引擎自身的函数调用栈,会一直执行到栈空为止;   
>fiber实现了资质的组件调用栈,以链表的形式遍历组件树,可以灵活的暂停,继续,丢弃执行的任务,使用浏览器的 requestIdlCallback 这一API,该API会在浏览器空闲时依次调用函数

react框架内部的运作分为3层:
>+ Virtual DOM层: 描述页面长什么样;
>+ reconciler层: 负责调用组件生命周期方法,进行Diff运算等;
>+ renderer层: 根据不同的平台,渲染出相应的页面,reactDOM,reactNative等;

react16优化了reconciler层,成为 fiber reconciler,旧版本对应称作stack reconciler; statck reconciler执行过程是不能被打断的,filer reconciler可以分段执行,执行一段时间之后,会把控制权交还给浏览器,为了达到这种效果需要有一个调度器(Scheduler)来进行任务分配;fiber执行过程分为2个阶段:
>+ 生成fiber树,得出需要更新的节点信息,这一步是渐进的过程可以被打断,让优先级更高的任务先执行;
>+ 将需要更新的节点一次批量更新,该过程不可以被打断;

fiber 树 
>fiber树在首次渲染的时候一次生成,在diff计算的时候,会比对已有树和最新VDOM,在VDOM的基础上增加额外信息生成一棵新的树;   
>这棵新树每生成一个节点,都会把控制权交还给主线程,检查有没有优先级更高的任务需要执行,如果没有,则继续构建新的树节点,若有,则fiber会丢弃正在生成的树,在空闲的时候重新执行;fiber树本质上是一个链表;
### diff算法

