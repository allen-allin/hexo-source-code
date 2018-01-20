---
title: 小技巧： 将vue-cli项目放在githubPages上在线预览
date: 2017-3-19 23:37:08
tags: 小tips
---

1. 将config/index.js中的assetsPublicPath改为相对路径  './'
```
//config/index.js

assetsPublicPath: './'
```


2. 命令行 npm run build
3. .gitignore文件中删除 .dist文件夹，即阻止对dist文件夹的忽略
4. 上传到github
5. 开启githu Pages
6. 记得在访问域名的时候，在原有URL的最后加上  /dist，而不是直接访问，
如： 我的一个vue项目的github Pages的域名为   aizys.win/myResumer ,这时候我直接访问是不行的，需要 访问这个域名：  aizys.win/myResumer/dist