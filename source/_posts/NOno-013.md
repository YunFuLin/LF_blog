---
title: Matery主题优化（一）
date: 2020-06-20 20:32:11
layout: 'archives'
urlname: blog
keywords: 'Matery主题优化（一）'
tags: 
- 个人博客
categories: 
- hexo
---

>特效：1、首页轮播冒泡  2、鼠标点击弹出文字 3、鼠标移动特效 4、动态标签栏
## 1、首页轮播冒泡
### 1、创建 bubble.js
在 **`themes>hexo-theme-matery>source>libs>others`** 新建一个文件 bubble.js
```javascript
// 首页轮播冒泡
function bubble () {
  $('.carousel-item, .pd-header').circleMagic({
    radius: 10,
    density: .2,
    color: 'rgba(255,255,255,.4)',
    clearOffset: 0.99
  });
} ! function (p) {
  p.fn.circleMagic = function (t) {
    var o, a, n, r, e = !0,
      i = [],
      d = p.extend({
        color: "rgba(255,0,0,.5)",
        radius: 10,
        density: .3,
        clearOffset: .2
      }, t),
      l = this[0];
    function c () {
      e = !(document.body.scrollTop > a)
    }
    function s () {
      o = l.clientWidth, a = l.clientHeight, l.height = a + "px", n.width = o, n.height = a
    }
    function h () {
      if (e)
        for (var t in r.clearRect(0, 0, o, a), i) i[t].draw();
      requestAnimationFrame(h)
    }
    function f () {
      var t = this;
      function e () {
        t.pos.x = Math.random() * o, t.pos.y = a + 100 * Math.random(), t.alpha = .1 + Math.random() * d.clearOffset,
          t.scale = .1 + .3 * Math.random(), t.speed = Math.random(), "random" === d.color ? t.color =
            "rgba(" + Math.floor(255 * Math.random()) + ", " + Math.floor(0 * Math.random()) + ", " + Math.floor(
              0 * Math.random()) + ", " + Math.random().toPrecision(2) + ")" : t.color = d.color
      }
      t.pos = {}, e(), this.draw = function () {
        t.alpha <= 0 && e(), t.pos.y -= t.speed, t.alpha -= 5e-4, r.beginPath(), r.arc(t.pos.x, t.pos.y,
          t.scale * d.radius, 0, 2 * Math.PI, !1), r.fillStyle = t.color, r.fill(), r.closePath()
      }
    } ! function () {
      o = l.offsetWidth, a = l.offsetHeight,
        function () {
          var t = document.createElement("canvas");
          t.id = "canvas";
          t.style.top = 0;
          t.style.left = 0
          t.style.right = 0
          t.style.zIndex = 0;
          t.style.position = "absolute";

          l.appendChild(t);
          t.parentElement.style.overflow = "hidden"
        }(), (n = document.getElementById("canvas")).width = o, n.height = a, r = n.getContext("2d");
      for (var t = 0; t < o * d.density; t++) {
        var e = new f;
        i.push(e)
      }
      h()
    }(), window.addEventListener("scroll", c, !1), window.addEventListener("resize", s, !1)
  }
}(jQuery);
bubble()
```
### 2、编辑配置文件
在主题的配置文件 `themes\hexo-theme-matery\_config.yml` 找到 `libs:` 然后在 js 下引入
```javascript
bubble: /libs/others/bubble.js
```
在主题的配置文件 `themes\hexo-theme-matery\_config.yml` 的底部，添加下面代码
```javascript
# 首页轮播冒泡
bubble:
  enable: true
```
### 3、引入文件
在文件 `themes\hexo-theme-matery\layout\layout.ejs` 的 `body` 标签里面 引入
```javascript
    <!-- 白色冒泡 -->
    <% if (theme.bubble.enable) { %>
      <script type="text/javascript" src="<%- theme.jsDelivr.url %><%- url_for(theme.libs.js.bubble) %>"></script>
    <% } %>
```
## 2、鼠标点击弹出文字
>鼠标点击弹出文字的步骤与实现冒泡的步骤相似，这次我发现了个简单的写法
### 1、创建 click_show_text.js
在主题文件 `themes\hexo-theme-matery\source\js` 下新建 `click_show_text.js`
```javascript
var a_idx = 0;
jQuery(document).ready(function($) {
    $("body").click(function(e) {
        var a = new Array
        ("富强", "民主", "文明", "和谐", "自由", "平等", "公正", "法治", "爱国", "敬业", "诚信", "友善");
        var $i = $("<span/>").text(a[a_idx]);
        a_idx = (a_idx + 1) % a.length;
        var x = e.pageX,
        y = e.pageY;
        $i.css({
            "z-index": 5,
            "top": y - 20,
            "left": x,
            "position": "absolute",
            "font-weight": "bold",
            "color": "rgb(" + ~~(255 * Math.random()) + "," + ~~(255 * Math.random()) + "," + ~~(255 * Math.random()) + ")"
        });
        $("body").append($i);
        $i.animate({
            "top": y - 180,
            "opacity": 0
        },
    3000,
    function() {
       $i.remove();
    });
    });
    setTimeout('delay()', 2000);
});

function delay() {
    $(".buryit").removeAttr("onclick");
}
```
### 2、直接引入文件
在文件 `themes\hexo-theme-matery\layout\layout.ejs` 的 `body` 标签里面 引入
```javascript
  <!--单击显示文字-->
  <script type="text/javascript" src="/js/click_show_text.js"></script>
```
## 3、鼠标移动特效
### 1、创建 mouse_snow.js
在主题文件 `themes\hexo-theme-matery\source\js` 下新建 `mouse_snow.js`
```javascript
(function() {
  function t() {
      i(),
      a()
  }
  function i() {
      document.addEventListener("mousemove", o),
      document.addEventListener("touchmove", e),
      document.addEventListener("touchstart", e),
      window.addEventListener("resize", n)
  }
  function n(t) {
      d = window.innerWidth,
      window.innerHeight
  }
  function e(t) {
      if (t.touches.length > 0)
          for (var i = 0; i < t.touches.length; i++)
              s(t.touches[i].clientX, t.touches[i].clientY, r[Math.floor(Math.random() * r.length)])
  }
  function o(t) {
      u.x = t.clientX,
      u.y = t.clientY,
      s(u.x, u.y, r[Math.floor(Math.random() * r.length)])
  }
  function s(t, i, n) {
      var e = new l;
      e.init(t, i, n),
      f.push(e)
  }
  function h() {
      for (var t = 0; t < f.length; t++)
          f[t].update();
      for (t = f.length - 1; t >= 0; t--)
          f[t].lifeSpan < 0 && (f[t].die(),
          f.splice(t, 1))
  }
  function a() {
      requestAnimationFrame(a),
      h()
  }
  function l() {
      this.character = "*",
      this.lifeSpan = 120,
      this.initialStyles = {
          position: "fixed",
          top: "0",
          display: "block",
          pointerEvents: "none",
          "z-index": "10000000",
          fontSize: "20px",
          "will-change": "transform"
      },
      this.init = function(t, i, n) {
          this.velocity = {
              x: (Math.random() < .5 ? -1 : 1) * (Math.random() / 2),
              y: 1
          },
          this.position = {
              x: t - 10,
              y: i - 20
          },
          this.initialStyles.color = n,
          this.element = document.createElement("span"),
          this.element.innerHTML = this.character,
          c(this.element, this.initialStyles),
          this.update(),
          document.body.appendChild(this.element)
      }
      ,
      this.update = function() {
          this.position.x += this.velocity.x,
          this.position.y += this.velocity.y,
          this.lifeSpan--,
          this.element.style.transform = "translate3d(" + this.position.x + "px," + this.position.y + "px,0) scale(" + this.lifeSpan / 120 + ")"
      }
      ,
      this.die = function() {
          this.element.parentNode.removeChild(this.element)
      }
  }
  function c(t, i) {
      for (var n in i)
          t.style[n] = i[n]
  }
  var r = ["#D61C59", "#E7D84B", "#1B8798"]
    , d = window.innerWidth
    , u = (window.innerHeight,
  {
      x: d / 2,
      y: d / 2
  })
    , f = [];
  t()
}
)();
```
### 2、直接引入文件
在文件 `themes\hexo-theme-matery\layout\layout.ejs` 的 `body` 标签里面 引入
```javascript
    <!-- 鼠标移动特效 -->
    <script type="text/javascript" src="/js/mouse_snow.js"></script>
```
## 4、动态标签栏
### 1、直接引入文件
在文件 `themes\hexo-theme-matery\layout\layout.ejs` 的 `body` 标签里面 引入
```javascript
    <!-- 动态标签栏 -->
    <script type="text/javascript">
      var OriginTitile = document.title, st; 
      document.addEventListener("visibilitychange", function () { 
        document.hidden ? (document.title = "爱我，别走！", clearTimeout(st)) : (document.title = "咦，你来啦！", st = setTimeout(function () { document.title = OriginTitile }, 3e3)) }) 
    </script>
```