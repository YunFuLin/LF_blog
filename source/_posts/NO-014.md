---
title: Matery主题优化（二）
date: 2020-06-22 23:10:11
layout: 'archives'
urlname: blog
keywords: 'Matery主题优化（二）'
tags: 
- 个人博客
categories: 
- hexo
---

>特效：1、页面加载动画 loading 2、看板娘（卡通人物）
## 1、页面加载动画 loading
### 1、创建 loading.ejs
在 **`themes\hexo-theme-matery\layout\_widget`** 下，新建一个文件 loading.ejs
```javascript
<% if (theme.preloader.enable) { %>
  <div id="loading-box">
    <div class="loading-left-bg"></div>
    <div class="loading-right-bg"></div>
    <div class="spinner-box">
      <div class="configure-border-1">
        <div class="configure-core"></div>
      </div>
      <div class="configure-border-2">
        <div class="configure-core"></div>
      </div>
      <div class="loading-word">加载中...</div>
    </div>
  </div>
  <!-- 页面加载动画 -->
  <script>
    $(document).ready(function () {
      document.body.style.overflow = 'auto';
      document.getElementById('loading-box').classList.add("loaded")
    })
  </script>
<% } %>
```
### 2、CSS代码
在 **`themes\hexo-theme-matery\source\css`** 下，新建一个文件 loading.css
```css
#loading-box .loading-left-bg,
#loading-box .loading-right-bg {
  position: fixed;
  z-index: 1000;
  width: 50%;
  height: 100%;
  background-color: #37474f;
  transition: all 0.5s;
}

#loading-box .loading-right-bg {
  right: 0;
}

#loading-box>.spinner-box {
  position: fixed;
  z-index: 1001;
  display: flex;
  justify-content: center;
  align-items: center;
  width: 100%;
  height: 100vh;
}

#loading-box .spinner-box .configure-border-1 {
  position: absolute;
  padding: 3px;
  width: 115px;
  height: 115px;
  background: #ffab91;
  animation: configure-clockwise 3s ease-in-out 0s infinite alternate;
}

#loading-box .spinner-box .configure-border-2 {
  left: -115px;
  padding: 3px;
  width: 115px;
  height: 115px;
  background: rgb(63, 249, 220);
  transform: rotate(45deg);
  animation: configure-xclockwise 3s ease-in-out 0s infinite alternate;
}

#loading-box .spinner-box .loading-word {
  position: absolute;
  color: #ffffff;
  font-size: 0.8rem;
}

#loading-box .spinner-box .configure-core {
  width: 100%;
  height: 100%;
  background-color: #37474f;
}

div.loaded div.loading-left-bg {
  transform: translate(-100%, 0);
}

div.loaded div.loading-right-bg {
  transform: translate(100%, 0);
}

div.loaded div.spinner-box {
  display: none !important;
}

@keyframes configure-clockwise {
  0% {
    transform: rotate(0);
  }

  25% {
    transform: rotate(90deg);
  }

  50% {
    transform: rotate(180deg);
  }

  75% {
    transform: rotate(270deg);
  }

  100% {
    transform: rotate(360deg);
  }
}

@keyframes configure-xclockwise {
  0% {
    transform: rotate(45deg);
  }

  25% {
    transform: rotate(-45deg);
  }

  50% {
    transform: rotate(-135deg);
  }

  75% {
    transform: rotate(-225deg);
  }

  100% {
    transform: rotate(-315deg);
  }
}
```
### 3、编辑配置文件
在主题的配置文件 `themes\hexo-theme-matery\_config.yml` ，末尾加入
```javascript
# 是否开启页面加载动画
preloader:
  enable: true
```
### 4、引入文件
在文件 `themes\hexo-theme-matery\layout\_partial\head.ejs` 的 `<head>` 标签里面 引入 `loading.css` 文件
```javascript
<link rel="stylesheet" type="text/css" href="<%- theme.jsDelivr.url %><%- url_for('/css/loading.css') %>">
```
在文件 `themes\hexo-theme-matery\layout\layout.ejs` 的 `body` 标签`第一行`引入
```javascript
<%- partial('_widget/loading') %>
```
>以上加载动画就大功告成啦！
## 2、看板娘（卡通人物）
### 1、安装模块
```javascript
yarn add hexo-helper-live2d  // 这个是必须的
yarn add live2d-widget-model-nico  // 安装自己喜欢的模型，这个可以更换
```
### 2、配置
在根目录的 `_config.yml`，底部添加
```javascript
# live2d
# https://github.com/EYHN/hexo-helper-live2d
live2d:
  enable: true # 是否启用看板娘
  scriptFrom: local # 默认
  pluginRootPath: live2dw/ # 插件在站点上的根目录(相对路径)，自动生成
  pluginJsPath: lib/ # 脚本文件相对与插件根目录路径
  pluginModelPath: assets/ # 模型文件相对与插件根目录路径
  # scriptFrom: jsdelivr    # jsdelivr CDN
  # scriptFrom: unpkg    # unpkg CDN
  # scriptFrom: https://cdn.jsdelivr.net/npm/live2d-widget@3.x/lib/L2Dwidget.min.js    # 你的自定义 url
  tagMode: false # 标签模式, 是否仅替换 live2d tag标签而非插入到所有页面中，具体见项目github描述
  debug: false # 调试, 是否在控制台输出日志
  model:
    use: live2d-widget-model-nico # live2d模型的名字
    scale: 1
    hHeadPos: 0.5
    vHeadPos: 0.618
  display:
    superSample: 2
    width: 200
    height: 400
    position: right # 左侧还是右侧
    hOffset: 100
    vOffset: -15 # 距底部距离
  mobile:
    show: false # 手机端是否可见
    scale: 0.5
  react:
    opacityDefault: 0.7
    opacityOnHover: 0.2
```
就是这么简单！