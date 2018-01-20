---
title: DOM文档加载顺序
date: 2017-4-13 20:52:46
tags: 学习心得
---

1. 解析HTML结构
2. 加载外部js、css文件
3. 解析并执行js、css代码
4. DOM构建完成，触发document.onDomContentLoaded事件，也就是jquery中的ready事件
5. 加载图片等外部资源
6. 页面加载完毕，触发window.onload事件