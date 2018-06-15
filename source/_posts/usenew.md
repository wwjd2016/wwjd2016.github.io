---
title: new的时候发生了什么？
date: 2018-06-15 14:17:24
categories: 前端
tags: javacript
---

起初，是神创造了天地，神说要有光，就有了光...，上帝靠着他自己的话语和能力，从无中生有，上帝到底是怎么实现的呢？这个是属于隐秘的事，我们无从得知。但是，我们在用js搬砖的过程中，哦，不！是改变世界的过程中，也经常只要一`new`就能`new`出来一个对象，那么这个`new`的过程中发生了什么呢？今天我们就来探讨一下。

在这之前我们先简单了解一下函数和原型链

#### 函数和构造函数

函数和构造函数有什么区别呢？

没区别（逗我玩呢？）。要说区别就是一般构造函数一般都是大驼峰命名，表明这是一个构造函数，用来new出来对象的。

函数创建时默认会有一个`prototype`属性，指向一个对象，被称为函数的原型对象，原型对象默认会有一个`constructor`属性，这个属性的值就是原函数。

不信请看代码：

```
// 首先创建一个函数，并给他加一个属性name
function Human(age, interest) {
  this.age = age;
  this.interest = interest;
}
// 打印出这个函数(对象)
Human.prototype.constructor === Human
// true
```
![Human](/imgs/Human.png)

#### 原型链

刚刚我们创造了一个人类的构造函数，下面我们新建一个实例：

```
const sanpang = new Human('不知道', '独裁')
// 打印出来
console.log(sanpang)
// 咦，好像有一个__proto__ (chrome下)属性，跟刚才的Human.prototype有点像
sanpang.__proto__ === Human.prototype
// true
```

![sanpang](/imgs/sanpang.png)

> `Human`类（其实js没有类的概念，但是为了方便理解，我们就把这个构造函数，称为类）和实例之间是共享`Human.prototype`这个对象的。

我们来测试一下：

```
//首先给Human类的原型对象增加一个属性：
Human.prototype.creator = 'God';
console.log(sanpang.creator) // God
// 给Human类的原型对象增加一个方法
Human.prototype.speek = function() {
  console.log('hello, world!')
}
sanpang.speek() //hello, world!
```

那也就是说，改变了`Human.prototype`，他的实例对象都会改变。这些定义在`Human.prototype`上的方法和属性，称为*原型方法和属性*，一般是一些公用的，不需要为每个实例都赋予的属性，这样会节省一些内存开支。

对于*实例方法和属性*，当然是定义在构造函数上的了。比如我们一开始定义的属性`interest`和`age`，每个实例都会保存一份。

```
const trunp = new Human(72, '和平')
trup.interest = '世界警察'
console.log(trup.interest) // 世界警察
console.log(sanpang.interest) // 独裁
```
所以，其实`类`和`实例`之间并没有直接的联系，仅仅是因为共享了原型对象，如果原型对象又是另外一个类的实例，那么原型对象就共享了另一个类的原型对象，一直往上，直到对象`Object`。

等等，所以new 的时候发生了什么？？

#### new的过程

- 创建一个新的对象
- 继承或者共享构造函数的prototype对象
- 为这个新的对象添加属性（执行构造函数中的代码）
- 如果构造函数返回了一个“对象”（包括数组，函数），那么这个对象会取代整个new出来的结果。如果构造函数没有返回对象，那么new出来的结果为步骤1创建的对象

所以，我们可以模拟一下：

```
const getClass = function (fn, ...args) {
  // 创建一个新的对象
  var o = {}
  // 共享构造函数的prototype对象
  o.__proto__ = fn.prototype;
  // 为新对象添加实例属性
  const f = fn.apply(o, ...args)
  // 返回实例对象
  return typeof f === 'object' ? f : o
}
const coder = getClass(Human, ['27', 'music'])
```

![coder](/imgs/coder.png)






