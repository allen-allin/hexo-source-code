---
title: 从React到vue：类比法学React
date: 2018-3-7 13:07:08
tags: React
description: 通过比较vue和React的相似点和不同点来更好的学习React
---

最近在学习React，因为有了vue的基础，所以理解起来不难，现将一些相似点和不同点整理出来

比较点 | vue | React 
- | :- | :- 
组件化 | 单文件组件/component选项| js文件
组件内部数据 | data | this.state 
计算属性 | computed | - 
监听器 | watch | 无，可使用自己写的[react-easy-watcher](https://www.npmjs.com/package/react-easy-watcher)实现 
列表渲染 | v-for |  JSX + 数组map
条件渲染 | v-if | JSX + 三元表达式 
父通信向子组件传值 | props | props 
子组件向父组件传值 | $emit |  props
组件内容分发 | slot | this.props.children 
状态管理 | vuex | Redux
代码复用 | mixin | 现在不支持mixin了，可使用HOC替代，参考[这里](https://github.com/MrErHu/blog/issues/4) 

