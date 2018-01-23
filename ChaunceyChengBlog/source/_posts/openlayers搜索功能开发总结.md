---
title: openlayers搜索功能开发总结
date: 2018-01-11 13:52:48
tags: [frontend, openlayers]
categories: language
---

> 这两天项目需要在地图上增加一个元素搜索的功能。在时间很赶的情况下，参考开源项目简单完成了此功能，期间有点小收获和心得，这里做个总结记录。

地图前端使用使用的时openlayers展示，地图上使用`ol.layer.Vector`的方式绘制增加了很多元素。需要在地图上增加一个通过元素名称搜索定位指定元素的功能。

## 参考开源项目
参考开源项目地址：[ol-geocoder](https://github.com/jonataswalker/ol-geocoder)  
功能演示：
![搜索地图](https://raw.githubusercontent.com/jonataswalker/ol-geocoder/screenshots/images/anim.gif)  

项目涉及文件时一个js和一个css，css主要负责按钮、输入框和显示框的样式。

## 功能开发  
现需要修改原搜索地图地址为搜索元素。开发过程不涉及复杂的东西。

## 心得总结
在使用开源项目的过程中把开源项目中的js文件基本上过了一下，还是学到了挺多东西，这里也正好做一个小结。
查询开源项目的源码也发现了自己确实存在很大的差距，但这这正好让

### 1. 立即执行函数
格式一：  
```javascript
(function (global, factory) {
   global.EleSearch = factory(global.ol);
}(this, (function （ol) {

}));
```
格式二：  
```javascript
(function (global, factory) {
   global.EleSearch = factory(global.ol);
})(this, (function (ol) {

}));
```
> 使用如上代码得到对象，而且代码里面的变量都是对象内部才能使用。这也是使用的js的函数闭包功能。

### 2. 类的继承（使用call函数）
参考代码：  
```javascript
(function (global, factory) {
   global.EleSearch = factory(global.ol);
}(this, (function （ol) {
    /**
     * @class Base
     * @extends {ol.control.Control}
     */
     var Base = (function (superclass) {
         function Base(options) {
             // ....
             superclass.call(this, {element: this.container});
         }
         if (superclass) Base.__proto__ = superclass;
         Base.prototype = Object.create(superclass && superclass.prototype);
         Base.prototype.constructor = Base;

         // 可以增加对象方法
         Base.prototype.getLayer = function getLayer() {
             // ...
         }
     })(ol.control.Control));
     return Base;
}));
```
### 3. let变量声明
使用前需要声明`use strict`，使用javascript`严格模式`；  
let和var声明变量的区别：  
1. let不能重复声明一个变量，var可以且值会改变；
2. 变量作用域不一样。
```javascript
(function() {
  var varTest = 'test var ok';
  let letTest = 'test let ok';

  {
    var varTest = 'varTest changed.';
    let letTest = 'letTest changed.';
  }

  console.log(varTest); // 输出：varTest changed.
  console.log(letTest); // 输出：test let ok
}());
```

### 4. 对象prototype属性
### 5. Promise对象
### 6. css namespace  
可以在所有css前面都加一个名称，如下：  
```css
.ol-elesearch .gcd-gl-expanded {
    width: 15.625em;
    height: 2.1875em; }
```
在所有css前面增加ol-elesearch作为namespace。

### 7. web js在线编辑器
这里主要介绍两个：
* jsrun  
[访问地址](http://jsrun.net)
使用`console.log()`会在右下角console dialog中显示值。但是如果使用浏览器的console窗口输出的参数数组。
* jsfiddle  
[访问地址](https://jsfiddle.net/)
js中console输出可以在浏览器的console窗口显示。
