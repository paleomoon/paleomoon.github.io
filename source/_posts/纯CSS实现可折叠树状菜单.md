---
title: 纯CSS实现可折叠树状菜单
date: 2017-04-07 18:16:27
tags: [CSS]
categories: CSS
---
上一篇文章通过CSS模拟了自定义下拉列表select样式，这里介绍的CSS实现可折叠树状菜单原理也类似。  
<!-- more -->  

## 原理
利用checkbox的checked值判断是否展开下级菜单。当未点击上级菜单，即checkbox未选中时，ol设置display:none隐藏；当点击上级菜单，checkbox选中，通过:checked伪类让ol展现出来。  

## 效果  
![效果](http://omcbb8o2a.bkt.clouddn.com/image/github/LearningFrontEnd/css/tree-list/style.jpg)  

## 代码
HTML结构
```html
<div class="tree-container">
    <ol>
        <li>
            <div class="node-container">
                <input type="checkbox" id="tree-node1" name="">
                <label for="tree-node1">文件一</label>
                <ol>
                    <li>
                        <div class="node-container">
                            <input type="checkbox" id="tree-node3" name="">
                            <label for="tree-node3">文件3</label>
                            <ol>
                                <li><a href="#">1</a></li>
                                <li><a href="#">2</a></li>
                                <li><a href="#">3</a></li>
                            </ol>
                        </div> 
                    </li>
                    <li><a href="#">1</a></li>
                    <li><a href="#">1</a></li>
                </ol>
            </div> 
        </li>
    </ol>
</div>
```
想要增加级数只需要在li中插入node-container类及其子元素:  
```html
<div class="node-container">
    <input type="checkbox" id="tree-node3" name="">
    <label for="tree-node3">文件3</label>
    <ol>
        <li><a href="#">1</a></li>
        <li><a href="#">2</a></li>
        <li><a href="#">3</a></li>
    </ol>
</div> 
```
## 兼容性
经测试兼容 IE10 、chrome 和现代移动浏览器。  

## DEMO
[预览地址](https://paleomoon.github.io/LearningFrontEnd/css/tree-list/index.html)  
[代码地址](https://github.com/paleomoon/LearningFrontEnd/tree/master/css/tree-list)  

## 参考
[纯CSS打造可折叠树状菜单 - CSDN](http://blog.csdn.net/woshisap/article/details/6679977)  
[树形菜单（纯CSS）](http://www.qdfuns.com/notes/17840/17c8c8e80567d5f7fbda3be89af9c7e5.html)