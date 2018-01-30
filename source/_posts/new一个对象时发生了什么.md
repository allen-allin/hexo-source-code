---
title: new一个对象时发生了什么
date: 2018-1-11 16:07:08
tags: new
description: 我们经常会new一个对象，那么这个过程到底发生了什么呢？ 
---

1. 创建一个空对象，这里用`this`给这个空对象命名，可以解释为什么构造函数中的`this`指向了新的生成的对象
```
var this = {}
```
2. 给这个空对象绑定原型，让它的`__proto__`指向构造函数的原型对象，形成原型链
```
this.__proto__ = constructor.prototype
```
3. 执行构造函数中的代码(为这个新对象添加属性)给`this`增加私有属性
4. 返回这个对象
```
return this
```