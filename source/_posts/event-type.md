---
title: 事件捕捉和冒泡
date: 2019-01-14 14:06:31
categories: 前端
tags: javacript
---

### 事件级别以及区别：

#### Dom0

注册方式：

```javascript
ele.onclick=function(){}
```

属于冒泡型事件。

#### Dom2

注册方式：

```javascript
ele.addEventListener(type, fun, boolean)
```

通过第三个参数指定事件类型，默认false，冒泡型

#### Dom3

增加了事件类型，自定义事件

### 事件流

- 捕捉
- 目标
- 冒泡

### 如何阻止事件冒泡

使用event对象

```javascript
e.stopPropagation()
```

### No code you say eggs!

```html
  <div id="outer">
    <div id="inner">点我</div>
  </div>
```

```javascript
var outer = document.getElementById('outer')
var inner = document.getElementById('inner')

document.addEventListener('click', function() {
  console.log('我是document')
})
outer.addEventListener('click', function() {
  console.log('我是outer')
}, true)
inner.addEventListener('click', function() {
  console.log('我是inner')
})
// 我是outer
// 我是inner
// 我是document
```

点击inner

- 事件分为两个阶段
- 第一阶段：事件捕获。浏览器从`document`开始，依次遍历，查找捕获型事件，当查找到`outer`时，发现是捕获事件，执行，然后继续查找，直到目标元素`inner`
- 第二阶段：事件冒泡。从目标元素转变为事件冒泡，依次向上查找冒泡型事件，直到`document`