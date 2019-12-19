---
title: TypeScript使用小技巧
categories: tool
tags:
  - ts
abbrlink: 6526f49
date: 2019-09-10 19:51:11
---

在学习过程中使用TypeScript还是遇到了很多巧妙的解决办法，下面整理一下近期遇到的很多问题，和最后使用的解决方案。有些技巧还是很实用的，节省了很多时间和精力。

<!-- more -->

### 如何在window 下面新添加（声明）一个新属性

我们知道TypeScript是强类型的，随便声明一个变量在全局对象window下就好编辑器报错，尝试若干次失败后，在网上找到了解决方法：

英文问题 [How do you explicitly set a new property on `window` in TypeScript?](https://stackoverflow.com/questions/12709074/how-do-you-explicitly-set-a-new-property-on-window-in-typescript)

```typescript
declare global {
    interface Window { MyNamespace: any; }
}
window.MyNamespace = window.MyNamespace || {};
```

### 当我使用 JavaScript 文件时，为什么我会得到 error TS5055: Cannot write file 'xxx.js' because it would overwrite input file 错误？

> 对于 TypeScript 文件来说，在默认情况下，编译器将在同一目录中生成与 JavaScript 相同文件名的文件。因为 TypeScript 文件与编译后的文件总是拥有不同的后缀，这么做是安全的。然而，如果你设置 `allowJs` 编译选项为 `true` 和没有设置任何的编译输出属性（`outFile` 和 `outDir`），编译器将会尝试使用相同的规则来编译文件，这将导致发出的 JavaScript 文件与源文件具有相同的文件名。为了避免意外覆盖源文件，编译器将会发出此警告，并跳过编写输出文件。
>
> 有多种方法可以解决此问题，但所有这些方法都涉及配置编译器选项，因此建议你在项目根目录中的 tsconfig.json 文件来启用此功能。如果你不想编译 JavaScript 文件，你只需要将 `allowJs` 选项设置为 `false`；如果你确实想要包含和编译这些 JavaScript 文件，你应该设置 `outDir` 或者 `outFile` 选项，定向到其他位置，这样他们就不会与源文件冲突。如果你仅仅是想包含这些 JavaScript 文件，但是不需要编译，设置 `noEmit` 选项为 `true` 可以跳过编译检查。

参考链接 [`tsconfig.json` 的行为](https://jkchao.github.io/typescript-book-chinese/faqs/tsconfig-behavior.html)

