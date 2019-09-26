---
title: TypeScript随机从数组中取出几个元素
categories: other
tags:
  - ts
abbrlink: c4d402bc
date: 2019-07-01 17:20:12
---

在游戏开发过程成随机是一种很常见的规则，有了随机，才有了彼此的不同，我们也经常会遇到从一个数组里面随机取出几个元素的需求，今天我也特意整理一下，以供以后参考。

<!-- more -->

### 从数组array中随机取出一个元素

假如 我们的数组是

```typescript
let array:Array<string> = ['1','2','4','5','6','7','8','9','10'];
```

那么实现的方法是：

```
let result = array[Math.floor(Math.random()*array.length)];
```

### 从数组array中中随机取几个元素

```typescript
function getRandomArrayElements(arr, count) {
    var shuffled = arr.slice(0), i = arr.length, min = i - count, temp, index;
    while (i-- > min) {
        index = Math.floor((i + 1) * Math.random());
        temp = shuffled[index];
        shuffled[index] = shuffled[i];
        shuffled[i] = temp;
    }
    return shuffled.slice(min);
}
var array = ['1','2','4','5','6','7','8','9','10'];
console.log( getRandomArrayElements(array, 5) );
```

