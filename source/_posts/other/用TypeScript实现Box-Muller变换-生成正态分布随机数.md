---
title: 用TypeScript实现Box-Muller变换-生成正态分布随机数
comments: true
categories: other
tags:
  - js
  - ts
abbrlink: 69c9f4cb
date: 2025-05-30 22:33:43
---

在开发游戏、金融模拟或科学计算应用时，我们经常需要使用**正态分布**（又称高斯分布）的随机数。虽然 `JavaScript` 原生提供了 `Math.random()` 生成**均匀分布**的随机数，但却无法直接得到正态分布的数据。

本文将介绍一种经典的转换方法 —— **Box-Muller 变换（Box-Muller Transform）**，并通过 `TypeScript` 实现它，用于生成符合指定均值（`mean`）和标准差（`stdDev`）的正态分布随机数。

<!--more-->

## Box-Muller 变换原理简述

Box-Muller 变换是将两个 [0,1) 区间内的独立均匀分布随机数转换为符合标准正态分布（均值为 0，标准差为 1）的随机数的方法之一。

公式如下：

```text
z0 = sqrt(-2 * ln(u1)) * cos(2π * u2)
z1 = sqrt(-2 * ln(u1)) * sin(2π * u2)
```

其中：

- `u1` 和 `u2` 是两个独立的均匀分布随机数；
- `z0` 和 `z1` 是独立的标准正态分布随机数。

我们这里只使用了 `z0`。

------

## TypeScript 实现

```typescript
function boxMullerTransform(mean: number = 0, stdDev: number = 1): number {
  let u1 = 0;
  let u2 = 0;

  // 保证 u1 不为 0
  while (u1 === 0) u1 = Math.random();
  while (u2 === 0) u2 = Math.random();

  // Box-Muller 变换
  const z0 = Math.sqrt(-2.0 * Math.log(u1)) * Math.cos(2.0 * Math.PI * u2);

  // 转换为指定均值和标准差的正态分布
  return z0 * stdDev + mean;
}
```

这个函数支持传入 `mean` 和 `stdDev` 参数，实现自定义正态分布。

## 生成并分析样本数据

我们生成 10,000 个样本，并计算样本的均值和标准差，验证生成结果的正确性：

```typescript
const randomNumbers = Array.from({ length: 10000 }, () => boxMullerTransform(0, 1));

const mean = randomNumbers.reduce((sum, val) => sum + val, 0) / randomNumbers.length;
const variance = randomNumbers.reduce((sum, val) => sum + Math.pow(val - mean, 2), 0) / randomNumbers.length;
const stdDev = Math.sqrt(variance);

console.log(`样本均值: ${mean}`);
console.log(`样本标准差: ${stdDev}`);
console.log('样本数据 (前10个):', randomNumbers.slice(0, 10));
```

### ✅ 输出示例：

```text
样本均值: -0.0168
样本标准差: 1.0051
样本数据 (前10个): [-0.299, 1.337, 1.375, -0.210, ...]
```

可以看到样本数据的均值非常接近 0，标准差非常接近 1，表明算法效果良好。

------

## 绘制直方图（Histogram）

我们可以简单地实现一个直方图生成器，以便分析数据分布的形态：

```typescript
function generateHistogram(data: number[], binCount: number = 50): void {
  const min = Math.min(...data);
  const max = Math.max(...data);
  const binSize = (max - min) / binCount;
  const bins = Array(binCount).fill(0);

  data.forEach(num => {
    const index = Math.min(Math.floor((num - min) / binSize), binCount - 1);
    bins[index]++;
  });

  console.log('直方图：', bins);
}

generateHistogram(randomNumbers);
```

### 示例输出（部分）：

```text
直方图：[3, 3, 7, 2, 19, ..., 1, 0, 2]
```

这个直方图展示了数值落在每个区间（bin）中的频数，整体呈现一个中间高两边低的“钟形曲线”，符合正态分布特征。

------

## 总结

这种生成正态分布随机数的方法非常适合用于模拟噪声、游戏角色属性分布、统计建模等场景。

------

####  附加阅读

- [TypeScript Math.random() 用法说明](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Math/random)