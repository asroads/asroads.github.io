---
title: ASTC纹理压缩介绍
comments: true
categories: tool
tags:
  - astc
  - 小游戏
mathjax: true
abbrlink: d11b313e
date: 2024-05-26 14:40:03
---

ASTC (Adaptive Scalable Texture Compression) 是一种高级纹理压缩技术，由 ARM 和 AMD 共同开发。ASTC 的设计目标是提供更高效的压缩率和更高的图像质量，并且支持多种块尺寸和位深度。ASTC 允许在压缩率和质量之间灵活选择，适用于各种不同的应用场景。ASTC广泛用于减少纹理占用的内存和带宽。ASTC 提供了多种块尺寸，如 6x6 和 8x8 块尺寸。不同的块尺寸对内存占用有不同的影响。
<!--more-->

## 介绍

### ASTC 主要特点

1. **多种块尺寸**：ASTC 支持多达 14 种不同的块尺寸，从 4x4 到 12x12 像素。
2. **高压缩效率**：ASTC 提供了比其他纹理压缩格式（如 DXT 和 ETC）更高的压缩效率。
3. **灵活性**：可以在不同的块尺寸和位深度之间进行选择，以适应不同的性能和质量需求。
4. **宽泛的硬件支持**：广泛支持现代的图形硬件，包括移动设备和 PC。

### ASTC 块尺寸

ASTC 支持以下块尺寸：

- 4x4
- 5x4
- 5x5
- 6x5
- 6x6
- 8x5
- 8x6
- 8x8
- 10x5
- 10x6
- 10x8
- 10x10
- 12x10
- 12x12

每个块的大小固定为 128 比特（16 字节）【通常是 128 比特或 16 字节】，但是每个块覆盖的像素数量不同。

### ASTC 内存占用大小

为了详细介绍 ASTC 的内存占用大小，需要从纹理的分辨率、使用的块尺寸和每个块的比特数（通常是 128 比特或 16 字节）开始。以下是详细的计算步骤和示例。

### 计算步骤

#### 1. 了解输入参数

- **纹理分辨率**：纹理的宽度 W 和高度 H。
- **块尺寸**：ASTC 块的宽度 BW 和高度 BH。
- **每个块的大小**：每个 ASTC 块固定为 128 比特（16 字节）。

#### 2. 计算块数量

- **横向块数**
  $$
  {横向块数} = \lceil \frac{W}{BW} \rceil
  $$
  
- **纵向块数**
  $$
  {纵向块数} = \lceil \frac{H}{BH} \rceil
  $$
  
- **总块数为**

$$
{总块数} = \left(\lceil \frac{W}{BW} \rceil \right) \times \left(\lceil \frac{H}{BH} \rceil \right)
$$



#### 3. 计算物理内存占用大小

每个块占用 16 字节，因此：
$$
{物理内存占用大小} = \text{总块数} \times 16 \text{ 字节}
$$


### 示例计算

假设我们有一张 1024x1024 像素的纹理，分别使用 ASTC 4x4、ASTC 6x6 和 ASTC 8x8 块尺寸，计算它们的内存占用大小。

#### 1. ASTC 4x4 块尺寸

**计算横向和纵向块数**：
$$
{横向块数} = \lceil \frac{1024}{4} \rceil = \lceil 256 \rceil = 256
$$

$$
{纵向块数} = \lceil \frac{1024}{4} \rceil = \lceil 256 \rceil = 256
$$

**计算总块数**：
$$
{总块数} = 256 \times 256 = 65536
$$
**计算物理内存占用大小**：
$$
{物理内存占用大小} = 65536 \times 16 \text{ 字节} = 1048576 \text{ 字节} \approx 1 \text{ MB}
$$

#### 2. 使用 ASTC 6x6 块尺寸

**计算横向和纵向块数**：
$$
{横向块数} = \lceil \frac{1024}{6} \rceil = \lceil 170.67 \rceil = 171
$$

$$
{纵向块数} = \lceil \frac{1024}{6} \rceil = \lceil 170.67 \rceil = 171
$$

**计算总块数**：
$$
{总块数} = 171 \times 171 = 29241
$$
**计算物理内存占用大小**：
$$
{物理内存占用大小} = 29241 \times 16 \text{ 字节} = 467856 \text{ 字节} \approx 457 \text{ KB}
$$


#### 3. 使用 ASTC 8x8 块尺寸

**计算横向和纵向块数**：
$$
{横向块数} = \lceil \frac{1024}{8} \rceil = \lceil 128 \rceil = 128
$$

$$
{纵向块数} = \lceil \frac{1024}{8} \rceil = \lceil 128 \rceil = 128
$$

**计算总块数**：
$$
{总块数} = 128 \times 128 = 16384
$$
**计算物理内存占用大小**：
$$
{物理内存占用大小} = 16384 \times 16 \text{ 字节} = 262144 \text{ 字节} \approx 256 \text{ KB}
$$


### ASTC 压缩原理及压缩率计算

#### 公式解释

- **像素大小**（bits）：指的是经过 ASTC 压缩后，每个像素所占的位数。公式为：
  $$
  {像素大小} = \frac{16 \text{ bytes}}{\text{分块宽高}} \times 8 \text{ bits/byte}
  $$
  这里，分块宽高是指一个 ASTC 块所覆盖的像素数。例如，4x4 块覆盖 16 个像素，8x8 块覆盖 64 个像素。

- **压缩率**：表示压缩前后的比率，即原始未压缩像素大小（32-bit）与压缩后像素大小的比率。公式为：
  $$
  {压缩率} = \frac{\text{原始像素大小（32-bit）}}{\text{像素大小}}
  $$
  
- **图像大小**：表示压缩后的图片大小。公式为：
  $$
  {图像大小} = \frac{\text{原始图片大小}}{\text{压缩率}}
  $$
  其中，原始图片大小是未压缩的图片大小。

### 示例计算

假设我们有一张 4 MB 的 1024x1024 像素的原始图片。以下是不同块尺寸下的计算过程：

#### 1. 4x4 块尺寸

- **像素大小**：
  $$
  {像素大小} = \frac{16 \text{ bytes}}{4 \times 4} \times 8 \text{ bits/byte} = \frac{16}{16} \times 8 = 8 \text{ bits}
  $$
  
- **压缩率**：
  $$
  {压缩率} = \frac{32 \text{ bits}}{8 \text{ bits}} = 4
  $$
  
- **压缩后图片大小**：
  $$
  {图像大小} = \frac{4 \text{ MB}}{4} = 1 \text{ MB}
  $$
  

#### 2. 5x5 块尺寸

- **像素大小**：
  $$
  {像素大小} = \frac{16 \text{ bytes}}{5 \times 5} \times 8 \text{ bits/byte} = \frac{16}{25} \times 8 = 5.12 \text{ bits}
  $$
  
- **压缩率**：
  $$
  {压缩率} = \frac{32 \text{ bits}}{5.12 \text{ bits}} = 6.25
  $$
  
- **压缩后图片大小**：
  $$
  {图像大小} = \frac{4 \text{ MB}}{6.25} \approx 655 \text{ KB}
  $$
  

#### 3. 6x6 块尺寸

- **像素大小**：
  $$
  {像素大小} = \frac{16 \text{ bytes}}{6 \times 6} \times 8 \text{ bits/byte} = \frac{16}{36} \times 8 \approx 3.56 \text{ bits}
  $$
  
- **压缩率**：
  $$
  {压缩率} = \frac{32 \text{ bits}}{3.56 \text{ bits}} \approx 8.99
  $$
  
- **压缩后图片大小**：
  $$
  {图像大小} = \frac{4 \text{ MB}}{8.99} \approx 455.62 \text{ KB}
  $$
  

#### 4. 8x8 块尺寸

- **像素大小**：
  $$
  {像素大小} = \frac{16 \text{ bytes}}{8 \times 8} \times 8 \text{ bits/byte} = \frac{16}{64} \times 8 = 2 \text{ bits}
  $$
  
- **压缩率**：
  $$
  {压缩率} = \frac{32 \text{ bits}}{2 \text{ bits}} = 16
  $$
  
- **压缩后图片大小**：
  $$
  {图像大小} = \frac{4 \text{ MB}}{16} = 256 \text{ KB}
  $$
  

#### 5. 10x10 块尺寸

- **像素大小**：
  $$
  {像素大小} = \frac{16 \text{ bytes}}{10 \times 10} \times 8 \text{ bits/byte} = \frac{16}{100} \times 8 = 1.28 \text{ bits}
  $$
  
- **压缩率**：
  $$
  {压缩率} = \frac{32 \text{ bits}}{1.28 \text{ bits}} = 25
  $$
  
- **压缩后图片大小**：
  $$
  {图像大小} = \frac{4 \text{ MB}}{25} \approx 163 \text{ KB}
  $$
  

#### 6. 12x12 块尺寸

- **像素大小**：
  $$
  {像素大小} = \frac{16 \text{ bytes}}{12 \times 12} \times 8 \text{ bits/byte} = \frac{16}{144} \times 8 \approx 0.89 \text{ bits}
  $$
  
- **压缩率**：
  $$
  {压缩率} = \frac{32 \text{ bits}}{0.89 \text{ bits}} \approx 35.95
  $$
  
- **压缩后图片大小**：
  $$
  {图像大小} = \frac{4 \text{ MB}}{35.95} \approx 113.93 \text{ KB}
  $$
  

#### 解释与理解

- **像素大小**：表示每个像素在经过 ASTC 压缩后的比特数。分块宽高越大，每个像素分摊到的比特数越少，意味着压缩效率越高。
- **压缩率**：原始像素大小与压缩后像素大小的比值。这个比值越大，表示压缩效果越好，压缩后的文件越小。
- **压缩后图片大小**：根据压缩率计算压缩后的文件大小。压缩率越大，压缩后文件越小。

## 拓展

ASTC 8x8 相对于 ASTC 6x6 的内存占用比例 = 2 比特 / 3.56 比特 ≈ 0.5618

### 推荐几款 ASTC 压缩工具：

1. **ASTC Encoder by ARM**：
   - [ARM ASTC Encoder](https://github.com/ARM-software/astc-encoder)
2. **Intel® Texture Works Plugin for Photoshop**：
   - [Intel® Texture Works Plugin](https://github.com/GameTechDev/Intel-Texture-Works-Plugin)
3. **KTX Software by Khronos Group**：
   - [KTX Software](https://github.com/KhronosGroup/KTX-Software)

### 总结

通过上述计算可以看出，不同的 ASTC 块尺寸对纹理的内存占用有显著影响：

- **ASTC 4x4**：每个块覆盖 16 像素，总内存占用大约为 1 MB，压缩率较低，但图像质量较高。
- **ASTC 6x6**：每个块覆盖 36 像素，总内存占用大约为 457 KB，压缩率适中，图像质量有所下降。
- **ASTC 8x8**：每个块覆盖 64 像素，总内存占用大约为 256 KB，压缩率较高，图像质量下降更明显。

在实际应用中，选择合适的块尺寸需要根据具体的性能和质量需求做出平衡。在需要更高质量的场景中可能选择较小的块尺寸（如 4x4），而在需要更高压缩率的场景中可能选择较大的块尺寸（如 8x8）。为了直观展示不同情况下的大小关系，我们列出一个表格：

| ASTC 块尺寸 | 文件存储大小 | CPU 内存占用大小（原始数据） | CPU 内存占用大小（压缩数据） | 显存占用大小 |
| ----------- | ------------ | ---------------------------- | ---------------------------- | ------------ |
| 4x4         | 0.5 MB       | 4 MB                         | 1 MB                         | 1 MB         |
| 6x6         | 0.5 MB       | 4 MB                         | 457 KB                       | 457 KB       |
| 8x8         | 0.5 MB       | 4 MB                         | 256 KB                       | 256 KB       |

从表中可以看出：

1. **文件存储大小**：ASTC 文件通常较小，取决于压缩率，示例中假设为 0.5 MB。
2. **CPU 内存占用大小**：未压缩的原始数据占用较大内存（4 MB），而压缩数据的大小接近显存占用大小。
3. **显存占用大小**：显存占用与块尺寸直接相关，块尺寸越大，占用的显存越小。