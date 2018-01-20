---
title: DOM操作-表格的添加删除搜索
date: 2016-12-10 14:20:49
tags: 学习心得
---
### 知识点
* DOM操作，添加删除子节点
* 搜索：精确搜索、忽略大小写搜索、模糊搜索、多关键字搜索
* 表格相关
### 思路
* 点击‘添加’按钮，添加一个行，在第一列添加id（每次都+1），在第二列添加第一个文本框中输入的内容，在第三列添加第二个文本框中输入的内容，在第四列添加一个用于删除该行的a链接，步骤如下：
1. 新建一个行元素<tr>，并且将这个新的<tr>添加为tBody的子节点
2. 新建第1个列元素<td>,它的内容为id++（需要提前声明id为已有行数+1），并且将这个新的<td>添加为刚才新建的<tr>的子节点
3. 新建第2个列元素<td>,它的内容为第一个文本框中输入的内容，并且将这个新的<td>添加为刚才新建的<tr>的子节点
4. 新建第3个列元素<td>,它的内容为第二个文本框中输入的内容，并且将这个新的<td>添加为刚才新建的<tr>的子节点
5. 新建第4个列元素<td>,它的内容一个用于删除该行的a链接，并且将这个新的<td>添加为刚才新建的<tr>的子节点

```
oBtn1.onclick=function() {
        var oTr=document.createElement('tr');
        oTab.tBodies[0].appendChild(oTr);

        var oTd=document.createElement('td');
        oTd.innerHTML=id++;
        oTr.appendChild(oTd);

        var oTd=document.createElement('td');
        oTd.innerHTML=oName.value;
        oName.value='';
        oTr.appendChild(oTd);


        var oTd=document.createElement('td');
        oTd.innerHTML=oAge.value;
        oAge.value='';
        oTr.appendChild(oTd);

        var oTd=document.createElement('td');
        oTd.innerHTML='<a href="#">删除</a>'
        oTr.appendChild(oTd);
 ｝
```
* 点击“删除”，将该行删除。由于“删除”列元素的子节点，而需要删除的元素是行，所以应该是从tBdoy中删除这个a链接的父级的父级

```
var aA=oTr.getElementsByTagName('a');
for (var i = 0; i < aA.length; i++) {
    aA[i].onclick=function() {
        oTab.tBodies[0].removeChild(this.parentNode.parentNode);
    }
}
```
需要注意的是，这些代码应该处于“添加”事件的函数中
* 点击“搜索”按钮，搜索表格的name一列中与文本框中内容一致的行，并先把所有行的颜色清空，然后将符合搜索条件的行的颜色变成黄色，根据搜索要求不同，分为以下几种：
1. 精确搜索：需要表格内容与搜索文本框中的内容一模一样

```
oBtn2.onclick=function() {
    for (var i = 0; i < oTab.tBodies[0].rows.length; i++) {
        //先把所有行的颜色都清空
        oTab.tBodies[0].rows[i].style.background='';
        var sTxt=oSearch.value;
        var sTab=oTab.tBodies[0].rows[i].cells[1].innerHTML;
        //如果搜索框中的内容和表格某一行的内容相同，则把这一行变黄
        if (sTxt==sTab) {       
            oTab.tBodies[0].rows[i].style.background='yellow';
        }
    }   
  }
```
2. 忽略大小写搜索：忽略大小写后，进行搜索

```
oBtn2.onclick=function() {
    for (var i = 0; i < oTab.tBodies[0].rows.length; i++) {
     
        oTab.tBodies[0].rows[i].style.background='';
        
        //把搜索框中的内容和表格内容都变成小写（或者大写也可以）
        var sTxt=oSearch.value.toLowerCase();
        var sTab=oTab.tBodies[0].rows[i].cells[1].innerHTML.toLowerCase();
    
        if (sTxt==sTab) {       
            oTab.tBodies[0].rows[i].style.background='yellow';
        }
    }   
  }
```
3. 模糊搜索：要求表格内容包含搜索内容。需要用到search方法。

   语法为：要搜查的对象.search(搜查内容)       
   若没有在要搜查的对象中找到搜查内容，则返回-1

```
oBtn2.onclick=function() {
    for (var i = 0; i < oTab.tBodies[0].rows.length; i++) {
     
        oTab.tBodies[0].rows[i].style.background='';
        
        //把搜索框中的内容和表格内容都变成小写（或者大写也可以）
        var sTxt=oSearch.value.toLowerCase();
        var sTab=oTab.tBodies[0].rows[i].cells[1].innerHTML.toLowerCase();
        //如果search方法返回的不是-1，说明搜索到了
        if (sTab.search(sTxt)!=-1) {        
            oTab.tBodies[0].rows[i].style.background='yellow';
        }
    }   
  }
```
4. 多关键字搜索：包含多个关键词的搜索，配合模糊搜索。需要用到split方法，通过指定的字符串或者符号（如空格）把一个字符串拆分成若干个子字符串然后将结果作为字符串数组返回。
    
    语法为：var arr=要被分解的字符串对象.split(指定的字符串)

```
oBtn2.onclick=function() {
    for (var i = 0; i < oTab.tBodies[0].rows.length; i++) {
        oTab.tBodies[0].rows[i].style.background='';
        var sTxt=oSearch.value.toLowerCase();
        var sTab=oTab.tBodies[0].rows[i].cells[1].innerHTML.toLowerCase();
        ////多关键字搜索，先把搜索框中的内容通过空格分解，然后加入数组
        var arr=sTxt.split(' ');  
        //对于数组中所有的子字符串，如果某行满足模糊搜索，则那一行就变黄
        for (var  j= 0; j < arr.length; j++) {
            if (sTab.search(arr[j]!=-1)) {
                oTab.tBodies[0].rows[i].style.background='yellow';              
            }
        }
    }
}
```



