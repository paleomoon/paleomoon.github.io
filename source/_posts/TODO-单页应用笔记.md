---
title: TODO 单页应用笔记
date: 2017-11-22 21:08:50
tags: [JavaScript]
categories: JavaScript
---
前段时间花了很多业余时间，完成了一个 TODO 页面，详见[这里](https://github.com/paleomoon/todo)。以下是一些总结。
<!-- more -->
## HTML与数据交互
一开始做这个todo应用时，是使用纯dom操作的，再加上使用的是原生js，那叫一个痛苦，想起用jQuery的幸福时光了（原谅小白只会jQuery）。但写了一些，发现太难操作数据了，代码耦合非常严重，查到张鑫旭大神的博客[js面向数据编程(DOP)一点分享](http://www.zhangxinxu.com/wordpress/?p=1384)，用HTML模板拼接，批量刷新，顿时想起还有这种操作，于是重写。dom操作对于很小的页面修改还可以，对于频繁的页面操作，还是使用HTML模板方便太多（不过听说现在都是使用什么双向数据绑定了，回头了解下）。  

更新：上述HTML模板其实是伪模板，HTML耦合在js代码中，不利于修改，数据的应用也不具有规律性，很麻烦，详见张鑫旭后来写的另一篇文章[基于HTML模板和JSON数据的JavaScript交互](http://www.zhangxinxu.com/wordpress/?p=2649)。

## 数据结构的设计和存储
数据使用JSON结构化，使用了数据库的思想。  

本地存储使用localStorage。
- 存储：`localStorage.setItem('item', content);`，一般都是存储字符串，所以需要将对象序列化：`JSON.stringify(obj)`。
- 取出：`localStorage.getItem('item')`，取出的仍然是字符串，需要反序列化：`JSON.parse(str)`。  

调试阶段，chrome中本地页面可能无法使用localStroge，在设置中启用就可以了。

## 对话框
模态框：对话框分为模态类型（modal）和非模态类型。如果打开的是模态窗口，那么你就只能对当前此窗口进行操作，而普通窗口则可以进行任意切换。alert()、confirm()、prompt()都属于模态框。   

半透明遮罩层呈现模态框：以前写了个demo见[这里](https://github.com/paleomoon/baidu-ife/tree/gh-pages/YaoyaoSchool/task6)，需要注意的是，半透明遮罩不要使用opacity（半透明遮罩上还要显示其他内容的话），而使用`background-color: rgba(0, 0, 0, 0.5);`,否则其上的内容也会是半透明的。  

confirm(message)用于显示一个带有指定消息和 OK 及取消按钮的对话框。如果用户点击确定按钮，则 confirm() 返回 true。如果点击取消按钮，则 confirm() 返回 false。  
prompt(text,defaultText)用于显示可提示用户进行输入的对话框。如果用户单击提示框的取消按钮，则返回 null。如果用户单击确认按钮，则返回输入字段当前显示的文本。

## 表单相关元素
js获取select标签选中的值  
```
var obj = document.getElementById(”testSelect”); //定位select
var index = obj.selectedIndex; // 选中索引
var text = obj.options[index].text; // 选中option的文本
var value = obj.options[index].value; // 选中option的value属性值
//或
var value = obj.value; //单选下拉列表框对象的value属性值就是选中项的value值
```

获取和设置textarea标签的值，dom中，只能用的value属性，不能用innerHTML。但html中没有value属性，所以html中设置值只能用标签包括。  

textarea设置成最大化样式：
```
textarea {
    width: 100%;
    height: 100%;
    resize: none;
    outline: none;
}
```

`<input id="meeting" type="date" value="2014-01-13"/>`,type实际上是可以为“date”、“week”、“month”、“time”、“datetime”和“datetime-local”。

## 两种立即执行函数
```
(function() {
//...
}( args ) );
//------------------------------------
(function() {
//...
})( args );
```

## 浮动元素垂直居中
父元素设置
```
display: table-cell;
vertical-align: middle;
```

其实这个页面并没有什么复杂的东西，就是其中数据结构一开始设计的不够好，后面参考了[这里](https://github.com/Gaohaoyang/baidu-ife-practice/tree/master/task0003)，表示感谢。这么个简单的东西我还是花了大量时间，暴露出来很多基础的问题，还是之前太浮躁，没有打牢基础。接下来需要重构、工程化以及适配移动端，要学的东西太多了。路漫漫其修远兮，坚持就是胜利。

更新：
## require.js重构
**事件代理**  
当使用html模板更新页面，需要重新绑定事件，这样非常麻烦，一种简单的方法就是使用事件代理：模板的容器元素监听事件，当点击某个元素，事件会冒泡到容器元素触发相应事件，然后通过listener.call(target, event)触发目标元素的事件。  
因为指定了目标元素，所以其子元素的事件不会触发，这时只能手动指定子元素了。
```
    var delegateEventByTagName = function (element, tag, eventName, listener) {
        addEvent(element, eventName, function (e) {
            var event = e || window.event;
            var target = event.target || event.srcElement;

            if (target.tagName.toUpperCase() === tag.toUpperCase()) {
                listener.call(target, event);
            }
            else if (target.parentNode.tagName.toUpperCase() === tag.toUpperCase()) { //子元素冒泡一层
                listener.call(target.parentNode, event);
            }
        });
    };
```
