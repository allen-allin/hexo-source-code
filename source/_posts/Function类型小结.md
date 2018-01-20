---
title: Function类型小结
date: 2016-12-23 09:37:08
tags: 学习心得
---
### 什么是Function类型
之前已经说过，变量的值有基本类型值和引用类型值，而Function类型就属于引用类型值的一种。说白了，Function类型就是函数。由于引用类型都是对象，所以函数实际上也是对象。既然是对象，就和其他引用类型一样有自己的属性和方法。既然是引用类型的值，函数名实际上也是一个指向函数的指针，而不会与某个函数绑定。
### 没有重载
因为函数名是指针，指向函数对象，所以当有几个函数名一模一样时，后面的会覆盖前面的函数，这个指针最终只指向最后一个函数对象。
### 定义函数的两种方法
1. 函数声明，格式如下
```
function function_name(arg0,arg1,arg2,...) {
        //函数体    
}
```
函数声明的重要特性就**是函数声明提升**，意思是系统在执行代码时会先读取函数声明，这样就可以把函数声明放在调用它的语句之后了。
2. 函数表达式

最常见的就是先创建一个匿名函数然后将这个匿名函数赋值给一个变量，变量名就是函数名。

```
var function_name=function (arg0,arg1,arg2,...) {
    //函数体 
}
```

匿名函数有一个重要特性就是，匿名函数的执行环境是全局环境，也就是说匿名函数里的this总是指window.
### 函数内部属性
每个函数内部都有两个特殊的对象：arguments和this
* arguments对象：类似数组，包含着传入函数的所有参数。arguments有一个属性callee，该属性是一个指针，指向拥有这个arguments对象的函数，即指向正在执行的函数。
比较经典的例子就是递归函数。

    递归函数是在一个函数通过名字调用自身的情况下构成的。在递归函数中，这样的做法会使函数的执行与函数名紧紧耦合在了一起，为了降低耦合度，一般用arguments.callee 来代替函数体中的函数名，arguments.callee是一个指向正在执行的函数的指针。这样即使执行函数的时候函数名发生了变化，也可以正常执行。

```
function factorial(num) {
    if (num<=1) {
        return 1;
    } else {
        return num*arguments.callee(num-1);     //用argument.callee代替函数名factorial
    }
}
```
* this对象：简单的说，当前方法或者函数属于谁，this就是谁。全局函数的this是window。
### 函数属性
每个函数都包含两个属性：length和prototype。

* length属性表示函数希望接受的命名参数的个数，即函数定义时圆括号里的参数个数。
* prototype属性在创建自定义引用类型以及实现继承时极为重要，prototype属性的不可枚举的，使用for-in无法发现
### 函数方法：
每个函数都包含apply()和call()方法，这两个方法是非继承的，也就是说是每个函数都自带的。
* apply()和call()方法的功能完全一样，都是把当前函数的this对象强行变成apply()或者call()方法的第一个参数。圆括号里从第二个参数开始才是原函数真正的参数。函数绑定了apply()或者call()方法后，立即执行。

```
window.color='red';
var obj1={ color:'blue'; }
function sayColor( ) {
    alert (this.color); //全局函数，this就是window
}
sayColor();//   弹出red

// 绑定call()或apply()后函数立即执行
sayColor.call(this);//弹出red,把原函数的this(window)变成了全局环境的this(window)，其实this本身没有改变，仍然是window
sayColor.call(window);//弹出red,把原函数的this(window)变成了window，其实this本身没有改变，仍然是window
sayColor.call(obj1);//弹出blue，把原函数的this(window)变成了obj1,而obj1.color就是blue
```

* apply()和call()方法的区别：接受参数的方式不同。
    * apply()的第一个参数是要改变的this，第二个参数是保存真正参数的数组arguments，也可以是一个具体的由参数组成的数组。
    * call()的第一个参数是要改变的this，从第二个开始，是原函数真正的参数，且传递的参数必须逐个列举出来
    
```
//以下两种apple()方法的传参方式等价
function_name.apply(this,arguments);
function_name.apply(this,[arg0,arg1,arg2,...]);

//以下是call()方法的传参方式：
function_name.call(this,arg0,arg1,arg2,...);
```
* bind()方法：作用类似与apply()和call()方法，但是不会立即执行。会返回一个函数，这个返回的函数的this值会被绑定为bind()的参数。

```
window.color='red';
var obj1={ color:'blue'; }
function sayColor( ) {
    alert (this.color); //全局函数，this就是window
}
var objectSayColor=sayColor.bind(obj1); // 返回一个函数，这个函数的this就是bind()括号里的参数obj1
objectSayColor();       //执行这个返回函数，this.color就是obj1.color,弹出blue
```
### 闭包
见本博客中的《闭包小结》
### 