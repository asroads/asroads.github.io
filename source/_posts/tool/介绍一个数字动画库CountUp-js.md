---
title: 介绍一个数字动画库CountUp.js
comments: true
categories: tool
tags:
  - js
abbrlink: 76506a91
date: 2020-03-05 15:30:56
---

动画是游戏内必必不可少的功能，在AS3时代强大的类库TweenMax，在H5页游时代，各大引擎都有了自己的相对于TweenMax的部分功能的实现，Cocos Creator 还结合了Action 做了很多拓展，但是有时候我们需要一些常见的功能比如对于数字变化的效果，此时如果自己写就要控制好很多参数，那么网上有没有轮子呢，答案是：有
<!--more-->

### 介绍

**CountUp.js** 是一个零依赖的轻量a级 JavaScript 库，可用于快速创建以更有趣的方式显示数字的动画。虽然这个是个H5页面的类库，但是不影响把他迁移到Cocos Creator 项目里。这个库有Typescript和JavaScript版本，还有演示的官网。可以说对于游戏开发者和想省事的开发者确实是个福音。

说干就干，接下来先放上两个链接：

- 代码库地址：[点击进入](https://github.com/inorganik/countUp.js)
- 演示地址：[点击进入](https://inorganik.github.io/countUp.js/)

演示地址样式：

![image-20200305155218350](介绍一个数字动画库CountUp-js/image-20200305155218350.png)

可以看出 有很多可以配置的参数和选项：

##### 参数:

- `target` =  html元素的id，input，svg text元素，或者计数发生时先前选择的元素/输入的var
- `startVal` = 你要开始的值
- `endVal` = 你想要达到的价值
- `decimals` = （可选）数字中的小数位数，默认为0
- `duration` = （可选）持续时间（以秒为单位），默认为2
- `options` = （可选，请参阅demo）格式化/easing选项对象

可以省略小数，持续时间和选项以使用默认值。

比如：

```javascript
var numAnim = new CountUp("SomeElementYouWantToAnimate", 24.02, 99.99);
numAnim.start();
```

或者：

```javascript
numAnim.start(someMethodToCallOnComplete);

// or an anonymous function
numAnim.start(function() {
    // do something
})
```

接下来说说如何修改 其实 就是修改 ：`target` 和 `printValue()` 这个方法：

`target`赋值 原代码：

```javascript
this.el = (typeof target === 'string') ? document.getElementById(target) : target;
```

修改为：

```javascript
this.el = target;
```

`printValue()` 方法 原代码：

```javascript
    CountUp.prototype.printValue = function (val) {
        var result = this.formattingFn(val);
        if (this.el.tagName === 'INPUT') {
            var input = this.el;
            input.value = result;
        }
        else if (this.el.tagName === 'text' || this.el.tagName === 'tspan') {
            this.el.textContent = result;
        }
        else {
            this.el.innerHTML = result;
        }
    };
```

修改为：

```javascript
    CountUp.prototype.printValue = function (val) {
        var result = this.formattingFn(val);
       	this.el.string = result;
    };
```

此时我们应该这样调用：

```javascript
const options = {
  startVal: 50,//开始的值
  decimalPlaces: 2,//小数点后几位
  duration: 2.0,//多久时间
  useGrouping: false,
};
let myTargetElement = XXXXX（类型为cc.Label类型）
let demo = new CountUp(myTargetElement, 94.62, options);
if (!demo.error) {
  demo.start();
} else {
  console.error(demo.error);
}
```

![number](介绍一个数字动画库CountUp-js/number.gif)

以上就是这个数字动画的用法。

### 参考

- [CountUp.js – 有趣的数字动画库](http://f2ex.cn/countup-js/)
- [CountUp.js Demo](https://inorganik.github.io/countUp.js/) 