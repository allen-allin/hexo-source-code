---
title: vue中的render函数、template选项、el选项、独立构建和运行时构建
date: 2017-3-25 15:27:53
tags: 学习心得
---
#### render函数，template选项和el选项
* render函数，官网是这样讲的：

>    Vue 推荐在绝大多数情况下使用 template 来创建你的
>   HTML。然而在一些场景中，你真的需要 JavaScript 的完全编程的能力，
>   这就是 render 函数，它比 template 更接近编译器。

所以说，render是一个函数，是一个可以编译HTML的函数，比template更接近编译器，这个函数的具体语法这里先不做讨论。

在网上查到的资料及vue官网的内容来看，render函数，template选项和el选项都是用来生成HTML模版的，不同之处就是三者的优先级不同：

```
render函数 > template > el
```

> 当Vue选项对象中有render渲染函数时，Vue构造函数将直接使用渲染函数渲染DOM树，当选项对象中没有render渲染函数时，Vue构造函数首先通过将template模板生成render函数，然后再渲染DOM树，而当选项对象中既没有render渲染函数，也没有template模板时，会通过el属性获取挂载元素的outerHTML来作为模板，并编译生成渲染函数。

简而言之，就是分为以下几种情况：
1. 如果有render函数，则Vue构造器就会忽略template直接使用render函数
2. 如果没有render函数，则Vue构造器先通过template模板生成render函数再渲染
3. 既没有render渲染函数，也没有template模板时，就通过el挂载的元素的outerHTML作为模板生成render函数

总之，进行DOM渲染的时候，**最终Vue构造器都将生成一个render函数**。

当然，构建Vue实例时，可以不含有render, template, el三者中任何一个。

#### 独立构建和运行时构建

在用vue-cli初始化项目的时候，会出现一个选项

```
? Vue build (Use arrow keys)
> Runtime + Compiler: recommended for most users
  Runtime-only: about 6KB lighter min+gzip, but templates (or any Vue-specific
  HTML) are ONLY allowed in .vue files - render functions are required elsewhere

```
其中，Runtime + Compiler 就是独立构建，Runtime-only就是运行时构建。

官网的说明：
> 运行时构建不含模板编译器，因此不支持template选项，只能用render选项，但即使使用运行时构建，在单文件组件中也依然可以写模板，因为单文件组件的模板在构建时预编译为render函数。

1. 如果选择了Runtime-only，则不能使用诸如这样的template选项了，只能使用render函数

```

//选了Runtime-only，出现以下代码就会报错
Vue.component('my-component', {
  template: '<div>A custom component!</div>'
})

```

但是在单文件组件中仍然可以使用template标签，这是因为单文件组件的<template>模板在构建时预编译为render函数，其最终还是一个render函数

```

//在.vue文件中可以使用<template> 模板
<template>  
    <div>Hello</div>
</template>
```
2. 如果选择了Runtime + Compiler ，则不用担心不能使用template选项，但是要比Runtime-only**重一点**
3. 如果不是用vue-cli,而是直接在HTML文件的script标签中引入vue.js，这时也和独立构建一样，可以使用template选项



