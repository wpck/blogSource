---
title: webpack入门学习
date: 2021-03-19 16:00:53
tags: 'webpack'
cover: ./img/15webpack.webp
---

 平时工作中使用react框架,及其脚手架工具,几乎没有自己手动配置过webpack,本篇结合webpack[官方文档](https://webpack.docschina.org/guides/getting-started/)来学习;

webpack是一个js应用程序的静态模块打包工具,处理应用程序时,在内部构建一个依赖图,映射到项目的各个模块,生成一个或多个bundle

### 核心概念
>1. 入口(entry)
只是webpack使用哪个模块座位构建内部依赖图的起点,默认是./src/index.js,可以通过webpack.config.js配置entry指定

>2. 输出(output)
指定在哪里输出创建的bundle,以及如何命名这些文件,默认是./dist; ./dist/main.js; 可以通过配置output属性指定

>3. 加载器(loader)
webpack只能理解js和json文件,loader的作用是处理其他类型的文件,并转换成有效模块,添加到依赖图中;     
loader有两个属性: test: 识别哪些文件要被转换;  use: 定义转换时应该使用哪个loader;      
lodader的执行顺序是从后向前;                    
第一个执行的loader接收源文件内容作为参数,其他loader接收前一个loader执行的返回值作为参数;
>> css-loader 和 style-loader的区别       
>css-loader: 引入.css文件,处理import/require/@import/url引入的css文件            
>style-loader: 使用style标签将css-loader内部样式注入到HTML页面中                 
>> file-loader 和 url-loader 加载图片文件           
>file-loader: 打包完成之后,每加载一次图片都会发送一次http请求;       
>url-loader: 会根据文件内容计算出一个base64编码的字符串,且会被打包进最终的bundle里,加载时直接从浏览器缓存获取;如果图片过大会影响首页加载速度;               

>4. 插件(plugin)
loader用来转换某些类型的模块,plugin用于执行范围更广的任务:打包优化,资源管理,注入环境变量等;使用插件要先require()引入,放在plugins数组中,使用new操作符来创建一个插件实例;       
html-webpack-plugin为应用程序创建一个html文件,并生成一个bundle;
>> plugin和loader的区别
>loader是一个转换器,操作的是文件
>plugin是一个扩展器,针对loader结束后,webpack打包的整个过程,不是操作文件,而是基于事件机制工作,监听webpack打包过程中的节点,执行某些任务;

>5. 模式(mode)
mode: 'development' | 'production'(默认) | 'none';        
指定webpack使用相应模式的内置优化;


webpack.config.js
```
const HtmlWebpackPlugin = require('html-webpack-plugin);  //需要npm安装
const webpack = require('webpack');
const path = require('path');

module.exports = {
    mode: 'production',   //内置优化模式
    entry: './path/file/entry.js',   //配置入口文件
    output: {   //指定输出的文件夹和文件名
        path: path.resolve(__dirname, 'dist),
        filename: 'bundle.js',
    }
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ['css-loader', 'style-loader']
            }, {
                test: /\.(gif|png|svg|jpeg)$/,
                use: [
                    {
                        loader: 'file-loader',
                        options: {
                            name: '[name].[ext]',   //输出文件名
                            outputPath: 'imgs',   //输出文件路径
                        }
                    }, 'url-loader'
                ]
            }
        ]
    }
    plugins: [
        new webpack.ProgressPlugin(),   //引用webpack内置plugin
        new HtmlWebpackPlugin({ template: './src/index.html' }),
    ]
}
```