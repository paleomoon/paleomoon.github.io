---
title: JavaScript日期选择组件的实现
date: 2017-06-19 21:06:39
tags: [JavaScript]
categories: JavaScript
---
实际网页开发中经常要用到各种日期组件，这里通过原生HTML/CSS/JavaScript完成一个日期选择器（datepicker）组件的开发。
<!-- more -->
效果如下：  
![效果](http://omcbb8o2a.bkt.clouddn.com/image/github-pages/datepicker/style.jpg)

基本功能：
1. 点击输入框出现日历。
2. 点击日期，输入框内容为选择的日期，日历收起。
3. 点击左右箭头翻月。

主要工作包括HTML静态结构的编写、日历数据的获取、组件的渲染以及组件事件的处理。 
# HTML结构
日历部分主要分为两部分：head区域（年月显示和月份切换）、body区域（星期和日期）。而body区域由table布局，星期为thead,日期为tbody。  
因为一个月最多可能跨越6周，所以tbody为42个单元格。  
样式编写在此省略。
# 日历数据的获取
获取一个月（日历页面）的所有数据的过程如下：
1. 一个月的天数
1/3/5/7/8/10/12月有31天，平年2月28天，闰年2月29天，其余月份30天。可以写一个函数根据年月获取某年某月有多少天。还有一种更简单的方法，本月（month-1）的天数=本月最后一天日期=下月（month）的第0天日期。同理，上月的天数=上月最后一天=本月的第0天。
```JavaScript
//下月的第0天
var curMonthLastDay = new Date(year, month, 0);
//本月的天数-本月最后一天
var curMonthDayNum = curMonthLastDay.getDate();
```

2. 需要显示的上一月的天数
星期按周日到周六排列，那么需 要显示的上一月的天数=本月的第一天的星期数（0-6）。

3. 需要显示的下一月的天数
需要显示的下一月的天数=42-本月的天数-上月显示的天数。

4. 根据以上数据即可获取一个日历页面的数据，保存在一个数组中，为下一步渲染提供数据。

# 生成TML结构
日历表格的结构复杂、内容变化频繁，我们采用动态生成DOM元素的方法，最后添加到页面。  
这一过程也简单，使用字符串拼接标签与上步获得的数据，生成HTML字符串。

# 渲染显示
将日历包含在一个wrapper容器中，将HTML结构添加在wrapper中，最后在body上添加wrapper。

# 事件处理
因为HTML内容是动态生成与删除，每个表格添加事件太麻烦，因此利用事件冒泡，将点击事件添加到wrapper容器。月份切换同理。

# 组件初始化
1. 定位
在使用时，只需要添加一个输入框，输入框可能出现在任何位置，为了让日历固定邻接输入框下方，需要在初始化时通过offsetLeft与offsetTop获取输入框的位置，以此使用绝对定位调整日历容器的位置。
2. 显示与隐藏
使用`wrapper.classList.add()`与`wrapper.classList.remove()`添加或移除类名控制日历的显示与隐藏。
3. 月份切换
事件处理函数中获取相应月份的数据，重新渲染显示。
4. 日期的点击选择
要在点击表格元素时获取对应日期，可以获取对应td的innerHtml，但这样当月显示的上月和下月日期获取会有错误。因此，在生成HTML结构时，为每个日期td添加自定义属性，存储准确的日期，通过target.dataset.propName可获取自定义data-propName属性的值。
5. 上下月日期显示灰色
生成HTML内容时为上下月添加控制样式的类即可。

# 组件化思想
为了模块不污染外部环境，将所有JS代码包裹在一个立即执行的匿名函数`(function () {})()`中，在函数中定义一个对象`var datepicker = {};`，唯一暴露给外界的对象只有该对象：`window.datepicker = datepicker;`。  
组件的容器类名可以取一个独特且比较长的名字，避免与外界产生冲突：`ui-datepicker-wrapper`，所有组件内部元素名称通过级联选择器包含在这个选择器内。

# 代码
[预览地址](https://jimhoo.github.io/components/DatePicker/index.html)  
[代码地址](https://github.com/JimHoo/components/tree/master/DatePicker)  

# 参考
[DatePicker组件开发 - 慕课网](http://www.imooc.com/learn/820)

