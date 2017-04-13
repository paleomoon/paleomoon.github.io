---
title: 纯CSS实现Tab选项卡
date: 2017-04-13 22:21:35
tags: [CSS]
categories: CSS
---
Tab选项卡很常见，一般都是通过js或jq实现，更灵活强大，但有时需求简单只用CSS也是可以实现的，也更简单方便。这里介绍两种方案。
<!-- more -->
# 通过a标签的锚点实现
这里又有两种具体实现方法：
## 纯锚点
### 原理
设置锚点链接指向相应div，将所有Tab指向的div放到一个固定宽高的容器里，并设置容器overflow:hidden，并且每个Tab页面的高度与容器保持一致。当点击锚点链接时，锚点对应的内容就会自动跳到可视范围以内。  
缺点：当CSS缺失的情况下无法清晰的阅读内容。
### 代码
html  
```html
<div id="box">
        <div id="tab-nav">
            <a href="#t1">tab1</a>
            <a href="#t2">tab2</a>
            <a href="#t3">tab3</a>
        </div>
        <div id="tab-content">
            <div id="t1">page1</div>
            <div id="t2">page2</div>
            <div id="t3">page3</div>
        </div>
    </div>
```
css  
```css
#box {
    display: inline-block;
}
#tab-nav a {
    text-decoration:none;
    border:1px solid #999; 
}
#tab-content {
    width:150px; 
    height:100px; 
    border:1px solid #999; 
    overflow:hidden;
}
#tab-content > div {
    width:100%; 
    height:100px;
}
```
### DEMO
[预览地址](https://jimhoo.github.io/LearningFrontEnd/css/tab/tab1-1.html)  
[代码地址](https://github.com/JimHoo/LearningFrontEnd/tree/master/css/tab/tab1-1.html)  
## 锚点 + :target
### 原理
可以通过点击锚链接触发指定元素的伪类:target，因此，可以将Tab页面内容隐藏，在:target伪类中设置Tab页面可见。这样在缺少CSS时依然可以清晰的阅读内容。  
html内容同上。  
### 代码
css  
```css
#box {
    display: inline-block;
}
#tab-nav a {
    text-decoration:none;
    border:1px solid #999; 
}
#tab-content {
    width:150px; 
    height:100px; 
    border:1px solid #999; 
}
#tab-content > div {
    display: none;
}
#tab-content > div:target {
    display: inline-block;
    width:100%; 
    height:100px;
}
```
### DEMO
[预览地址](https://jimhoo.github.io/LearningFrontEnd/css/tab/tab1-2.html)  
[代码地址](https://github.com/JimHoo/LearningFrontEnd/tree/master/css/tab/tab1-2.html)  
# 通过radio及:checked伪类实现
## 原理
隐藏所有Tab内容及input,显示label并关联相应input，当点击相应label选中radio,通过radio的:checked伪类显示相应Tab内容。  
## 代码
html
```html
<div class="box">
    <ul>
        <li>
            <input type="radio" id="item1" name="item" checked></input>
            <label for="item1">tab1</label>
            <div class="table-container">page1</div>
        </li>
        <li>
            <input type="radio" id="item2" name="item"></input>
            <label for="item2">tab2</label>
            <div class="table-container">page2</div>
        </li>
        <li>
            <input type="radio" id="item3" name="item"></input>
            <label for="item3">tab3</label>
            <div class="table-container">page3</div>
        </li>
    </ul>
</div>
```
css  
```css
.box {
    width: 400px;
    height: 370px;
}
.box .table-container {
    display: none;
    z-index: 1;
}
.box ul {
    position: relative;
    overflow: visible;
    height: 30px;
    margin: 0;
    padding-left: 30px;
}
.box li {
    display: inline-block;
    float: left;
    width: 80px;
    margin-right: 6px;
    list-style: none;
}
.box li label {
    display: block;
    width: 80px;
    height: 30px;
    border: 1px solid #fff;/*背景色*/
    border-top-right-radius: 6px;
    border-top-left-radius: 6px;
    text-align: center;
    line-height: 30px;
    background-color: #f5f5f5;
    cursor: pointer;
}
.box li input {
    display: none;
}
.box input:checked ~ label {
    position: relative;/*设置z-index*/
    border: 1px solid #ccc;
    border-bottom: 1px solid #fff;
    background-color: #fff;
    z-index: 2;
}
.box input:checked ~ .table-container {
    display: block;
    position: absolute;
    top: 31px;/*border-top与label重合*/
    left: 30px;
    width: 340px;
    height: 240px;
    border: 1px solid #ccc;
}
```
## 效果
![效果](http://omcbb8o2a.bkt.clouddn.com/image/github/LearningFrontEnd/css/tab/tab2-style.jpg)
## DEMO
[预览地址](https://jimhoo.github.io/LearningFrontEnd/css/tab/tab2.html)  
[代码地址](https://github.com/JimHoo/LearningFrontEnd/tree/master/css/tab/tab2.html)  

# 参考
[css实现tab切换 - CSDN](http://blog.csdn.net/jvid_sky/article/details/53260616)  
