---
title: 高性能遍历对象：拒绝for...in
date: 2018-2-12 12:07:08
tags: 性能
---

####  `for...in`的弊端
 常用的遍历对象的方法是`for-in`，但是这样遍历对象时执行速度很慢，因为使用`for...in`时遍历一个对象或者数组时，所有内部属性（包括继承的属性）和方法都会被遍历到，比如，对象原型的toString方法，以及数组的length属性。所以使用`for...in`会有两个弊端：
 1. 大多数时候，我们只关心对象自身的属性，而操作中引入继承的属性会让问题复杂化
 2. 执行速度很慢

 可以使用如下方式替换`for...in`循环

#### 使用ES6进行高性能的遍历
* 只需要遍历对象的属性时： `Object.keys(obj)`
* 只需要遍历对象的键值时： `Object.values(obj)` 
* 对象的属性和键值都需要遍历时： 
    1. 使用`Object.entries(obj)`

    ```javascript
        const obj = {name: 'xxx',age: 24}
        Object.entries(obj).forEach(v => {
            console.log({
                key: v[0],
                value: v[1]
            })
        })
        //输出 {key: "name", value: "xxx"}
        //     {key: "age", value: 24}
    ```
    2.如果觉得使用`Object.entries(obj)`时语义不够明确，那么可以使用ES6的`for...of`方法，以达到类似`for...in`的写法

    ```javascript
        const obj = {name: 'xxx',age: 24}
        const keys = Object.keys(obj)    //for...of不能直接遍历对象，所以需要对象的属性组成的数组

        for(let key of keys) {
            console.log({
                key,
                value: obj[key]
            })
        }
        //输出 {key: "name", value: "xxx"}
        //     {key: "age", value: 24}
    ```
    **注意**：`for...of`不能直接遍历对象，只能遍历具备`iterator`接口的结构，比如数组、Set 和 Map 结构、某些类数组对象（比如arguments对象、DOM NodeList 对象）、Generator。


