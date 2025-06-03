---
title: LCG随机数生成算法
comments: true
categories: other
tags:
  - js
  - ts
abbrlink: 87b7357a
date: 2025-06-02 00:16:05
---

为什么选择这些数字 9301、49297、233280 作为随机数生成参数，这些数字常用于线性同余生成器（Linear Congruential Generator，LCG）中的随机数生成算法。LCG 是一种经典的伪随机数生成方法。
<!--more-->

## 为什么选择这些数字？

**周期性与均匀性**

- 这些参数是经过精心选择的，使得 LCG 具有较长的周期（在最佳情况下，周期长度为 ）。
- 保证随机数在 0 到  之间均匀分布。

**互质性条件**

- 为了确保 LCG 能产生最长的周期，通常要求  和  互质，且  能被  的所有质因子整除。这些参数满足这些数学性质。

**效率**

- 使用这些数字计算时，特别是在低级语言或嵌入式系统中，位运算和模运算的效率较高。

**实用性**

- 这个数字是一个常见的选择，因为它接近 ，利于硬件优化。

### 例子

下面是一个基于线性同余生成器（LCG）实现的 TypeScript 版本的随机数生成器：

```typescript
class LCG {
  private seed: number;
  private readonly a: number = 9301;
  private readonly c: number = 49297;
  private readonly m: number = 233280;

  constructor(seed: number = Date.now()) {
    this.seed = seed % this.m;
  }

  // 生成 0 到 1 之间的随机数
  next(): number {
    this.seed = (this.a * this.seed + this.c) % this.m;
    return this.seed / this.m;
  }

  // 生成指定范围的随机数
  nextInt(min: number, max: number): number {
    return Math.floor(this.next() * (max - min + 1) + min);
  }
}

// 使用示例
const rng = new LCG(12345);
console.log(rng.next());      // 输出一个 0 到 1 之间的随机数
console.log(rng.nextInt(1, 100)); // 输出一个 1 到 100 之间的随机整数

```

实现包括：

- `next()`：返回一个 0 到 1 之间的随机数。
- `nextInt(min, max)`：生成一个指定范围内的随机整数。

### 统计概率例子

```typescript
class LCG {
  private seed: number;
  private readonly a: number = 9301;
  private readonly c: number = 49297;
  private readonly m: number = 233280;

  constructor(seed: number = Date.now()) {
    this.seed = seed % this.m;
  }

  // 生成 0 到 1 之间的随机数
  next(): number {
    this.seed = (this.a * this.seed + this.c) % this.m;
    return this.seed / this.m;
  }

  // 生成指定范围的随机数
  nextInt(min: number, max: number): number {
    return Math.floor(this.next() * (max - min + 1) + min);
  }
}

// 使用示例
const rng = new LCG(12345);

// 统计1到5的出现次数
const count: Record<number, number> = {1: 0, 2: 0, 3: 0, 4: 0, 5: 0};

for (let i = 0; i < 10000; i++) {
  const num = rng.nextInt(1, 5);
  count[num]++;
}

console.log('1到5的出现次数：', count);

// 计算概率
const probabilities = Object.fromEntries(
  Object.entries(count).map(([key, value]) => [key, (value / 10000 * 100).toFixed(2) + '%'])
);

console.log('1到5的出现概率：', probabilities);

```

## 总结

这些参数是经典的 LCG 选择，旨在提供较好的随机性、长周期和计算效率。在实际场景中，如果需要更高质量的随机数，通常会使用其他更复杂的算法，例如 Mersenne Twister 或 PCG。但对于简单场景或对随机性要求不高的应用，像这样的 LCG 仍然是一个不错的选择。