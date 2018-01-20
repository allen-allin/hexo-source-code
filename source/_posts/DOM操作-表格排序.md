---
title: DOM操作-表格排序
date: 2016-12-08 14:20:49
tags: 学习心得
---
# DOM操作-表格排序
## 知识点：
* 表格相关：表格主体tBodies、行rows、单元格cells及它们的快捷获取
* innerHTML方法
* 数组排序sort()方法及sort（）括号中的函数
* DOM添加子节点的方法appendChild()

## 思路：

1.表格的样式：
* 可用css来设置表格的border-collaspe属性，这个属性的默认值为separate,表示每个单元格之间有空隙，可以将这个属性的值设置为collaspe让空隙消失
* 必须在页面布局的时候在**table标签中设置行间样式边框border**，最好同时设置一个宽高，让表格好看一点
        
    
```
<table width="500px" border='1px solid black' > <table>
```


2.进行表格布局的时候最好thead、tbody、tfoot都要有，虽然thead和tfoot看起来可有可无，但是如果没有的话有时候会出错，**尤其是thead**

3.在JS中，表格的tBodies(注意是复数，因为tbody可能不止一个)、行rows、单元格cells不用事先用docement声明，可以直接获取使用

    
```
    var oTab=document.getElementsByTagName('table')[0];
    var oCells=oTab.tBodies[0].rows[0].cells;//表示表格内容中第1行的单元格
```

     
4.对表格第一列的内容排序：先定义一个空数组，然后把表格中所有的行都放进数组里
，即数组的成员就是所有的行
   
    
```
var arr=[];
    for (var i = 0; i < oTab.tBodies[0].rows.length; i++) {
        arr[i]=oTab.tBodies[0].rows[i];
    }
```


再根据所有行的**第一个单元格的内容**对这个数组进行排序

```
arr.sort(function (a1,a2) {  //参数a1和a2 取自该数组arr
   //由于要对第一列进行排序，就要把第一列（cells[0]的内容(innerHTML)转换为数字
        var n1=parseInt(a1.cells[0].innerHTML);
        var n2=parseInt(a2.cells[0].innerHTML);
        return n1-n2;
        })
```
注意：sort()方法括号里的函数的参数就是**取自这个数组**

5.把排好序的数组（按第一个单元格内容排序之后的所有的行rows）添加为tBodies[0]的子节点。

appendChild的功能：
* 若子节点是创建出的新元素，则将子节点添加到指定的父节点
* **若子节点原本就是存在的，则把该子节点从原父级删除然后添加到指定父级**

这里用的是第二种功能

```
//别忘了使用循环！！
for (var i = 0; i < arr.length; i++) {
    oTab.tBodies[0].appendChild(arr[i]);
    }
```

