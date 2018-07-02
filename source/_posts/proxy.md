---
title: 代理模式
date: 2018-07-02 15:01:44
categories: 前端
tags: 设计模式
---
代理模式是为一个对象提供一个代用品或占位符，以便控制对它的访问，当客户访问的时候，直接访问的是代理，而不是本体，这样代理可以帮助本体挡住很多不必要的事情。运用到程序当中就是：解耦功能、减少访问次数，节约资源等。

> `客户` => `代理` => `本体`

#### 图片预加载

在图片没有加载出来之前，先设置一张loading图占位，防止页面留白。

```
// 用来在html中插入一张图片
const myImg = (function(){
  const imgNode = document.createElement('img')
  document.body.appendChild(imgNode)
  return {
    setSrc: function(src) {
      imgNode.src = src;
    }
  }
})()

// 图片代理
const proxyImg = (function(){
  const img = new Image
  img.onload = function() {
    myImg.setSrc(this.src)
  }
  return {
    setSrc: function(src) {
      // 为图片设置一个loading状态
      myImg.setSrc('/loading.png');
      img.src = src;
    }
  }
})()

proxyImg.setSrc('https://createjs.com/assets/images/required/coguz.png')
```

#### 减少网络请求次数

对于一些频繁的、并且不要求即使生效的操作，可以间隔一段时间集中发送请求，可以节省后端资源。

```
    // 网络请求方法
    const requestHttp = function(ids) {
      console.log(`id：${ids}已经发送到服务器！`)
    }
    // 设置网络请求代理
    const proxyRequest = (function() {
      // 缓存需要的请求
      let cache = [], timer;
      return function(id) {
        cache.push(id)
        if(timer) {
          return;
        }
        // 设置每2s请求一次
        timer = setTimeout(function() {
          requestHttp(cache)
          clearTimeout(timer);
          timer = null;
          cache.length = 0;
        }, 2000)
      }
    })()

    for(let i = 1;i<1000; i++) {
      if(i>500) {
        setTimeout(function() {
          proxyRequest(i)
        }, 2000)
      } else {
        proxyRequest(i)
      }
    }
```