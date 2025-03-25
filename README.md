# 博客主题
基于[hexo-theme-matery](https://github.com/blinkfox/hexo-theme-matery)，做了一些个性化定制。

## 我的博客演示地址
[https://yunfulin.github.io/LF_blog/](https://yunfulin.github.io/LF_blog/)

## Hexo创建文章
```sh
hexo n 'no-001'
```

## 编辑配置文件后，重跑项目
```sh
hexo clean  #清除缓存文件 (db.json) 和已生成的静态文件 (public)。
hexo g  #生成静态文件。
hexo server   #启动服务器。 默认情况下，访问网址为： http://localhost:4000/。
```

## 启动项目
```sh
pnpm install
pnpm run server
```