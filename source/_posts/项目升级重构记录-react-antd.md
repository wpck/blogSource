---
title: 项目升级重构记录(react,antd)
date: 2020-10-11 20:49:34
tags: react
cover: ./img/3upgrade.jpg
description: react升级
---
## 升级重构
升级采用的方式是,直接使用 react-create-app 命令,初始化一个新项目,并安装最新稳定版本的依赖,初始化完成之后,拷贝 package.json 文件至原来项目,重新安装依赖;   涉及到的主要更新包括:   
react 15.6.1>16.13.1 && react-router 2.8.0>5.2.0 && redux 3.7.2>4.0.5 && react-redux 5.0.6>7.2.1 && antd 3.23.1>4.6.2

#### 1. antd
antd 图标更新: 取代原来<Icon type="">的格式,需要安装图标库: npm install --save @ant-design/icons

#### 2. react-router
+ 三种组件类型: routers(BroswerRouter,HashRouter); route(Route, Switch); navigation(Link,NavLink,Redirect);
+ 所有用到的webapp的组件都要从react-router-dom里引入
+ BrowerRouter 和 HashRouter 的区别是在于存储URL的方式和与web服务器的通信方式:   
>BroswerRouter存储常规的URL路径,要求服务器也要正确配置访问路径;   
>HashRouter存储URL的hash部分(#后面的部分,不与服务器交互),不需要服务器的特殊配置;   
+ 路由匹配器: Route Switch   
   当Switch渲染时,它会从其子Route中查找匹配的路由,只会渲染匹配到的Router,而忽略其他路由  
   >因此先指定Route,若匹配不到会渲染null;     
   >Route匹配的是URL的开始部分("/"会匹配所有路由,因此要把跟路由放在最后,或者使用exact字段表示精确匹配路由)
+ react router hooks(react >= 16.8支持)(useHistory useLocation useParams useRouteMatch) 
+ 路由渲染方式: component render children   
   >当使用行内函数渲染路由时,使用render,若此时使用component,则每次加载路由都会渲染一个新的组件;    
   >children render接收相同的prop,可以根据是否匹配动态的调整渲染的组件;     
   >children渲染优先于component渲染优先于render,故每个route只是用一种渲染方式
#### 3. 设置react本地启动端口:
1. package.json文件里: 在 scripts.start 命令上加上 "set PORT=3301 && "
2. 在项目目录下scripts/start.js里设置 DEFAULT_PORT = 3301;






   



技术水平的提升:对js,css的特性由熟悉变为精通,并在项目中不断应用新的API,Promise.all/each();less变量,函数等;
学习能力的提升:学习新框架的时间成本缩短,并未尝试过的技术栈和新兴技术有着强烈的好奇心;
思维能力的提升:对某个表象bug,会研究到其底层的原因;熟悉react和vue的底层实现原理,及其各自的优缺点和使用场景;
源码阅读能力的提升:会主动fork开源的仓库源码,了解其实现原理,比如antd,electron;

## 开发笔记
行内元素设置宽高,内外边距无效; 支持设置水平方向边距,不支持设置垂直方向边距;
iconfont高度被拉升bug:可能是项目里某个图标导致整个项目的图标高度都不正常;

### 前端安全问题
1. XSS(跨站脚本攻击) - 通过用户输入或其他方式,向我们的代码中注入了一段攻击js脚本,在未防范的状态下执行了这段脚本   
    反射型(通过url来注入) 存储型(将xss代码发送给了服务端,前端请求时,又返回给前端,常见于富文本编辑器)   
    XSS防范手段:  
    1. encode   
        html encode: < -'&lt'; > -'&gt'; ' -'&#39'; " -'&quot'; & -'&amp';  
        js encode: 使用 / 对特殊字符进行转义
    2. filter 防范富文本输入的攻击,白名单过滤:  
        引入白名单过滤库
2.CSRF(跨站伪造请求攻击) - 在钓鱼网站登录之后,获取用户的登录信息,去模拟另外一个网站的请求   
    CSRF防范手段:  
    1. method post提交时判断referer(get不会带有该字段),表示当前请求的来源页,通过该字段,判断来源是否安全;
    2. token 服务端生成一个token传递给前端,前端保存,每次访问携带该token,服务端比较是否一致;
