---
title: Cocos Creator2.4.x项目修改代码脚本页面自动刷新
comments: true
categories: game
tags:
  - Cocos
abbrlink: 9f2c6b8d
date: 2025-01-26 12:47:02
---

使用Cocos Creator开发项目的时候，很多时候，我们修改了代码后，需要点击一下Cocos Creator才能是最新的修改生效，那有没有办法我们修改后就跳过Cocos Creator点击的这个步骤呢？答案是可以的，下面就详细说说如何操作。

<!--more-->

# 详细步骤

首先我们先新建一个项目测试，这里已经新建完成，名字叫做 `onSaveRunProject`

## VScode

1. 首先我们按照`Cocos Creator`的文档基本操作 把`VScode` 工作流程搞定

![image-20241225134342665](Cocos-Creator2-4-x%E9%A1%B9%E7%9B%AE%E4%BF%AE%E6%94%B9%E4%BB%A3%E7%A0%81%E8%84%9A%E6%9C%AC%E9%A1%B5%E9%9D%A2%E8%87%AA%E5%8A%A8%E5%88%B7%E6%96%B0/image-20241225134342665.png)

![image-20241225134606461](Cocos-Creator2-4-x%E9%A1%B9%E7%9B%AE%E4%BF%AE%E6%94%B9%E4%BB%A3%E7%A0%81%E8%84%9A%E6%9C%AC%E9%A1%B5%E9%9D%A2%E8%87%AA%E5%8A%A8%E5%88%B7%E6%96%B0/image-20241225134606461.png)

2. 然后我们打开我们的项目 此时会有一个 `.vscode` 目录

![image-20241225134647307](Cocos-Creator2-4-x%E9%A1%B9%E7%9B%AE%E4%BF%AE%E6%94%B9%E4%BB%A3%E7%A0%81%E8%84%9A%E6%9C%AC%E9%A1%B5%E9%9D%A2%E8%87%AA%E5%8A%A8%E5%88%B7%E6%96%B0/image-20241225134647307.png)

3. `Run on Save` 是一个非常实用的 VS Code 扩展，它允许在保存文件时自动运行指定的命令。**安装 Run on Save**
   1. 打开 `VSCode`。
   2. 按 `Ctrl+P` 或 `Cmd+P`，然后输入 `ext install emeraldwalk.runonsave` 并按回车键。
   3. 或者，在左侧扩展市场中搜索 "Run on Save"，点击安装。

![image-20241225134716585](Cocos-Creator2-4-x%E9%A1%B9%E7%9B%AE%E4%BF%AE%E6%94%B9%E4%BB%A3%E7%A0%81%E8%84%9A%E6%9C%AC%E9%A1%B5%E9%9D%A2%E8%87%AA%E5%8A%A8%E5%88%B7%E6%96%B0/image-20241225134716585.png)

4. 在刚刚到`.vscode`目录下，打开 `settings.json` 文件，如果没有新建一个`settings.json` 并添加以下内容：

```json
{
    "emeraldwalk.runonsave": {
      "message": "Starting tasks...",
      "messageAfter": "All tasks completed!",
      "showElapsed": true,
      "commands": [
        {
          "match": "\\.(ts|js)$",
          "cmd": "curl http://localhost:7456/update-db",
          "message": "Triggering Cocos Creator resource update...",
          "messageAfter": "Resource update triggered!",
          "isAsync": false
        }
      ]
    }
  }
  
```

5. 此时我们的配置已经结束，注意：

```
// 3.x 自动刷新地址 "curl http://localhost:7456/asset-db/refresh"              
// 2.x 自动刷新地址请修改为 "curl http://localhost:7456/update-db"
```

### 验证结果

1. 我们修改 `HelloWorld.ts` 

```typescript
const {ccclass, property} = cc._decorator;

@ccclass
export default class Helloworld extends cc.Component {

    @property(cc.Label)
    label: cc.Label = null;

    @property
    text: string = 'hello';

    start () {
        // init logic
        this.label.string = this.text+"-"+this.text;
    }
}
```

2. 发现出问题

![image-20241225141032951](Cocos-Creator2-4-x%E9%A1%B9%E7%9B%AE%E4%BF%AE%E6%94%B9%E4%BB%A3%E7%A0%81%E8%84%9A%E6%9C%AC%E9%A1%B5%E9%9D%A2%E8%87%AA%E5%8A%A8%E5%88%B7%E6%96%B0/image-20241225141032951.png)

## webstorm

#### **使用 File Watchers**

WebStorm 提供了 File Watchers 功能，可以监视文件更改并执行特定命令。

#### **具体步骤**

1. **打开 File Watchers 配置**
   - 菜单栏 -> `File` -> `Settings` -> 搜索 `File Watchers`。
   - 点击右侧的 `+` 按钮，选择 `Custom`。
2. **配置 File Watcher** 在弹出的配置窗口中填写以下内容：
   - **Name**: 任意名称，例如 `Cocos Refresh`.
   - **File Type**: 选择要监控的文件类型，例如 `TypeScript` 或 `JavaScript`。
   - **Scope**: 选择监视范围，默认是 `Project Files`。
   - **Program**: 填写 `curl`（命令的执行程序）。
   - **Arguments**: 填写 `http://localhost:7456/update-db`（命令的参数）。
   - **Output Paths**: 留空。

![image-20241225142012751](Cocos-Creator2-4-x%E9%A1%B9%E7%9B%AE%E4%BF%AE%E6%94%B9%E4%BB%A3%E7%A0%81%E8%84%9A%E6%9C%AC%E9%A1%B5%E9%9D%A2%E8%87%AA%E5%8A%A8%E5%88%B7%E6%96%B0/image-20241225142012751.png)

1. **保存配置并启用**

   - 点击 `OK` 保存配置。
   - 确保 File Watchers 功能已启用。

2. **验证功能**

   - 修改并保存一个 `.ts`文件，观察页面变化。

   ```
   const {ccclass, property} = cc._decorator;
   
   @ccclass
   export default class Helloworld extends cc.Component {
   
       @property(cc.Label)
       label: cc.Label = null;
   
       @property
       text: string = 'hello';
   
       start () {
           // init logic
           this.label.string = this.text+"%%%%%"+this.text;
       }
   }
   ```
	
### 验证结果










   ![image-20241225142414656](Cocos-Creator2-4-x%E9%A1%B9%E7%9B%AE%E4%BF%AE%E6%94%B9%E4%BB%A3%E7%A0%81%E8%84%9A%E6%9C%AC%E9%A1%B5%E9%9D%A2%E8%87%AA%E5%8A%A8%E5%88%B7%E6%96%B0/image-20241225142414656.png)

## 参考

- [V7投稿 | vscode插件< cocosAutomaticRefresh > 保存代码自动刷新运行游戏 / 开发更省心](https://forum.cocos.org/t/topic/157204)