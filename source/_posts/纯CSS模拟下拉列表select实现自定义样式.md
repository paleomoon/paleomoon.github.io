---
title: 纯CSS模拟下拉列表select实现自定义样式
date: 2017-03-28 22:05:34
tags: [CSS]
categories: CSS
---
下拉列表 select 是一个 bom 元素，因此要美化它的时候仅仅依靠 css 无法实现。这里有两种方案：
<!-- more -->  
## 通过覆盖 select 元素来实现  

原理：  
将默认的下拉列表样式清除，自定义样式，并添加一张向右对齐的小箭头图片。  

```css
option {
    background-color: #be4e48;
}
select {
    width: 236px;
    height: 36px;
    /*Chrome和Firefox里面的边框是不一样的，所以复写了一下*/
    border: 1px solid #be4e48;
    /*将默认的select选择框样式清除*/
    appearance:none;
    -moz-appearance:none;
    -webkit-appearance:none;
    /*在选择框的最右侧中间显示小箭头图片*/
    background: #fff url(right_arrow.png) no-repeat right center;
    padding-right: 14px;
}
/*清除ie的默认选择框样式清除，隐藏下拉箭头*/
select::-ms-expand {
    display: none; 
}
```

缺点：option 样式无法修改，包括宽高，这个真的无解。

## 使用 radio 模拟下拉列表
网上找到的一篇博客，原理很简单但对于我这样的初学者实现起来也是很麻烦。大致理解了并进行了部分修改，有点乱。  

效果：  
![效果](http://omcbb8o2a.bkt.clouddn.com/image/github-pages/cs_select.JPG)  

原理：
- 将所有 li 使用绝对定位置于 div 触发器下,设置 z-index 使 li 被遮挡，
- 当鼠标移上触发 hover 选择器，这时对 li 使用相对定位回到正常文档流，同时父元素高度随之增加，呈现的便是展开的 select 样式。
- 当鼠标点击选项，实际上是点击 radio 对应的 label,这样 radio 便被选中，通过 input:checked 选择器将 label 的 z-index 增大。
- 鼠标移出后各元素位置回到最初状态，显示的是 label 选项。
- radio 自始至终不可见。  

1.html
```html
<fieldset class="radio-container">
    <div class="radio-options">
        <div class="toggle">城市</div>
        <ul>
           <li>
                <input type="radio" name="city" id="BeiJing" value="BeiJing">
                <label for="BeiJing"><span ></span>北京</label>
            </li>
            <li>
                <input type="radio" name="city" id="ShangHai" value="ShangHai">
                <label for="ShangHai"><span ></span>上海</label>
            </li>
            <li>
                <input type="radio" name="city" id="ShenZhen" value="ShenZhen">
                <label for="ShenZhen"><span ></span>深圳</label>
            </li>
            <li>
                <input type="radio" name="city" id="NewYork" value="NewYork">
                <label for="NewYork"><span ></span>纽约</label>
            </li>
              
        </ul>
    </div>
</fieldset>
```
2.css
这里省略了一些 css 代码,完整的可在最后的 github 链接查看
```css
radio-container {
    position: relative;
    height: 4em; /* 3em (being the max-height of the inner container) + 1em ("margin") */
 }
.radio-container:hover {
    z-index: 9999; 
}
.radio-options {
    position: absolute;
    max-height: 3em;
    width: 100%;
    overflow: hidden;
    transition: 0.7s;
}
.radio-options:hover {
  max-height: 100em;
}
.radio-options .toggle {
    position: relative;
    cursor: pointer;
    padding: 0.75em;
    background: darkgreen;
    border-radius: 10px;
    z-index: 1; 
}
/* li are stacked at the same position as .toggle, only .toggle is visible */
  .radio-options li {
    position: absolute;
    top: 0;
    left: 0;
    width: 100%;
    height: 100%;
  }
  .radio-options label {
    display: block;
    opacity: 0;
    transition: 0s; 
}
.radio-options input {
    position: absolute;
    top: 0;
    left: 0;
    width: 300px;
    height: 3em;
    opacity: 0;
    z-index:1;
    cursor: pointer;
}
```
3.鼠标移上时
```css
/* li elements have a normal flow within the .radio-options container */
.radio-options:hover li {
    position: relative; 
}
.radio-options:hover label {
    opacity: 1;
    transition: 0.5s; 
}
```
4.选中时
radio checked 但鼠标没触发时
```css
.radio-options input:checked ~ label {
   position: absolute;
   top: 0;
   left: 0;
   right: 0;
   opacity: 1;
/* is above the .toggle so is visible */
   z-index: 2;
/* has tha same styles as .toggle */
   padding: 0.75em;
   background: darkgreen;
   border-radius: 10px; 
}
```
鼠标移上时
```css
.radio-options:hover input:checked ~ label {
    position: static;
    border-radius: 0; 
}
```

以上就是纯 css 实现自定义下拉列表的方法，经测试兼容 IE10 和 chrome。移动浏览器和 IE8 的兼容性可以参考原文。  

缺点：  
选中选项后无法自动收起，必须鼠标移出后才可以。因为 css 没有父选择器，触发 checked 选择器后无法操作 li,要解决只能借助js了。  

我们看到，使用纯 css 代码很多，比较复杂。在实际项目中还是使用js或一些插件较好，否则有点得不偿失。  

[预览地址](https://jimhoo.github.io/LearningFrontEnd/css/index.html)  
[代码地址](https://github.com/JimHoo/LearningFrontEnd/tree/master/css)  

参考：  
[Css-only Alternative to the Select Element](https://pepsized.com/css-only-alternative-to-the-select-element/?utm_source=tuicool&utm_medium=referral)  
[How to style a select dropdown with CSS only without JavaScript - stackoverflow](http://stackoverflow.com/questions/1895476/how-to-style-a-select-dropdown-with-css-only-without-javascript)  
[浅谈CSS自定义下拉列表的样式](http://blog.csdn.net/zhouziyu2011/article/details/53729732)













