---
title: butterfly主题配置
date: 2019-11-17 20:18:40
tags: hexo
description: 记录个人博客的主题配置项
cover: ./img/2theme.jpg
---
### butterfly主题配置
选好主题搭建起博客之后,个性化配置自己的页面, 主题相关的配置在 / > theme > butterfly > _config.myl 文件
#### 1.创建tags about等页面
>_config.yml 配置好 menu 字段之后, 点击标签/关于, 未找到对应页面,需要新建    
>hexo new page tags/about -该命令会创建对应的 /source/tags(about)/index.md   
>type - 标签页(tags), 分类页(category), 友链页()link 必须配置   
>top_img, keywords, description等
#### 2.全局配置项   
>post_copyright -底部版权模块配置,enable表示是否显示该模块   
>footer -博客底部设置: since -站点起始时间; custom_text -自定义页脚文本; 
>aside -侧边栏设置: card_author -个人中心; card_announcement: -公告
>highlight_theme -代码高亮模式
>social -社交图标: 书写格式为: 图标名: url || 描述
#### 3.post文章设置
>tags -文章标签   
>top_img - 文章顶部图片   
>keywords -关键字   
>cover - 封面

#### 4.个人中心
>avatar - 个人中心头像
