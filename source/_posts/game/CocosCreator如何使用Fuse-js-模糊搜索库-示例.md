---
title: CocosCreator如何使用Fuse.js(模糊搜索库)示例
comments: true
categories: game
tags:
  - Cocos
abbrlink: b24b86e4
date: 2024-11-10 17:40:57
---

最近无意间发现了一个非常好用的轻量工具库，Fuse.js，支持模糊搜索。感觉还是非常好用的，于是想着是否可以引入Cocos Creator中使用，发现还是很简单的，于是就想着写下来，方便日后查阅。
<!--more-->

## Fuse.js是什么

强大、轻量级的模糊搜索库，没有任何依赖关系。https://www.fusejs.io/

### 什么是模糊搜索？

一般来说，模糊搜索（更正式的名称是近似字符串匹配）是查找与给定模式近似相等（而不是完全相等）的字符串的技术。

## Fuse.js的使用场景

它可能不适用于所有情况，但根据您的搜索要求，它可能是最理想的。例如：

- 当您想要对小型到中等大型数据集进行客户端模糊搜索时
- 当您无法证明设置专用后端只是为了处理搜索时

## Fuse.js的使用

这里使用最新版本 :https://github.com/krisk/Fuse/releases/tag/v7.0.0

#### 环境版本

- Mac
- Cocos Creator 2.4.13
- Fuse.js 7.0.0

#### 目录结构

![image-20241111174623316](./CocosCreator如何使用Fuse-js-模糊搜索库-示例/image-20241111174623316.png)

![image-20241111174656832](./CocosCreator如何使用Fuse-js-模糊搜索库-示例/image-20241111174656832.png)

#### 核心代码：

```typescript
import * as Fuse from "../libs/fuse";


const {ccclass, property} = cc._decorator;

@ccclass
export default class Game extends cc.Component {

    @property(cc.Label)
    label: cc.Label = null;

    @property
    text: string = 'hello';


    start () {
        // init logic
        this.label.string = this.text;
        console.log("cc.assetManager->", cc.assetManager);
// 示例：搜索含有 "Fire" 的角色
        this.testSearchCharacter();
    }

    private testSearchCharacter(){
        console.log("testHandler");
        // 定义数据类型
        type Character = {
            name: string;
            skill: string;
            level: number;
        };

// 创建一个角色数组作为示例数据
        const characters: Character[] = [
            { name: "Li Lei", skill: "Fireball", level: 5 },
            { name: "Fireball Lei", skill: "Fireball", level: 5 },
            { name: "Han Meimei", skill: "Ice Blast", level: 8 },
            { name: "Xiao Ming", skill: "Thunder Strike", level: 10 },
            { name: "Wang Wei", skill: "Healing Light", level: 6 }
        ];

// 设置 Fuse.js 配置
        const options = {
            keys: ["name", "skill"], // 指定要搜索的字段
            threshold: 0.3 // 匹配阈值，值越低越精确
        };

// 创建 Fuse 实例
        const fuse =  new (Fuse as any)(characters, options);
        this.searchCharacter("Fire",fuse);
    }

    /**
     * // 定义一个搜索函数
     * @param query
     * @private
     */
    private searchCharacter(query: string,fuse) {
        const results = fuse.search(query);
        console.log("smile:🚀 ~ f:", JSON.stringify(results));
        console.log("搜索结果:", results.map(result => result.item));
    }
}

```

#### 输出结果

![image-20241111174951299](./CocosCreator如何使用Fuse-js-模糊搜索库-示例/image-20241111174951299.png)

## 总结

其实如何使用还是比较简单的，库的文件也比较小，真的算得上轻量级了，如果游戏项目中有这样的需求，可以使用。