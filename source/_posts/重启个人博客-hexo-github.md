---
title: 重启个人博客(hexo+github)
date: 2019-11-16 20:26:40
tags: hexo
cover: hexo.png
description: 利用hexo搭建博客,托管到github上
---
### 重启个人blog
首先记录一下常用的markdown语法:   
> 标题: # -一级标题; ## -二级标题; ### -三级标题...以此类推;   
> 文字样式: **加粗**    
> 分割线: 三个或以上的 - 或 * ; 无序列表: - 或 + 或 * ; 有序列表: 数字.(1. 2.)    
> 代码: 单行代码: `` 两个反引号包括; 多行代码块:``` 三个反引号分别独占一行包括
```
**加粗**  *斜体*  ***斜体加粗***   ~加删除线的文字~
>一级嵌套引用  >>二级嵌套引用
图片: [图片alt](图片url 图片title)
超链接: [超链接名称](超链接地址 超链接title)
```

早在两年前使用github-pages搭建过个人博客,由于种种原因一直出于荒废的状态,最近闲来有时间想重启自己的博客.对于wordPress和hexo之后,了解到hexo支持更多个性化配置,本人动手能力比较强,故选择则hexo练手,此篇记录一下详细的搭建过程.

hexo是一款基于node.js的静态博客框架,可以方便的生成静态网页托管在github和heroku上

#### 1.github上创建仓库
>登录github,创建名为"用户名.github.io"的仓库;
#### 2.安装hexo
>本地安装git和nodejs环境;创建blog文件夹,并进入命令行,全局安装hexo脚手架;    
`npm install -g hexo-cli`

#### 3. 初始化博客及常用命令
`hexo init blog`   
```
hexo new/n 'title'  -新建文章,生成新的md博客文件
hexo generate/g    -生成
hexo server/s    -启动服务预览,可以本地预览
hexo deploy/d    -部署
hexo clean    -清除缓存
```
#### 4.更换主题
>1. 去[hexo官方主题](https://hexo.io/themes/)网站选择一款喜欢的主题: [Fluid](https://hexo.fluid-dev.com/), [ButterFly](https://butterfly.js.org/), [Ocean](https://zhwangart.github.io/) 这几个是功能比较齐全,使用推荐人数比较多,这次我选择了ButterFly
>2. 安装配置主题   
根据[安装文档](https://butterfly.js.org/posts/21cfbf15/#%E5%AE%89%E8%A3%9D)下载并使用主题    
git clone -b master https://github.com/jerryc127/hexo-theme-butterfly.git themes/butterfly    
修改根目录下配置文件 _config.yml theme: butterfly
>3. 安装插件   
本地启动博客并预览,打开预览页报错,缺少pug及stylus渲染器,下载并安装
npm install hexo-renderer-pug hexo-renderer-stylus --save
>4. 个性化配置(参考官方网站[butterfly配置](https://butterfly.js.org/posts/21cfbf15/))   
>+ 页面全部配置,使用根目录下_config.yml   
语言: language: zh-CN 
title -页面标题; language -语言;   
>+ 主题相关配置,使用 / > themes > butterfly > _config.yml   
顶部菜单: 配置 menu 字段   
顶部背景图: index_img -首页背景图(top_img); default_top_img - 页面的top_img没有配置时,会展示default_top_img;          

关于 butterfly 的更多个性化配置,单独放在一篇文章里.
#### 5.hexo使用本地图片
+ 修改主页配置文件_config.yml: post_asset_folder: true
+ 安装上传本地图片的插件: npm install hexo-asset-image --save
#### 6.部署发布 
连接github
>本地配置github账号信息
```
git config --global user.name 'github用户名'
git config --global user.email 'github邮箱账号'
```
>创建秘钥SSH: 
>ssh-keygen -t rsa -C "邮箱账号" -会生成本地.ssh文件夹   
>匹配秘钥: git Bash 输入 cat ~/.ssh/id_rsa.pub 获取本地秘钥(或者通过创建SSH时本地生成文件夹获取); 登录 github > settings > SSH key and GPG keys ,把本地秘钥粘贴到SSH; 输入 ssh -T git@github.com 命令检测是否连接成功  

配置 hexo deploy
>安装 deployer-git 插件: npm install hexo-deployer-git --save   
>修改 _config.yml 配置文件的 deploy字段
```
deploy: type: git
repository: https://github.com/wpck/wpck.github.io.git
branch: master
```

 






>



