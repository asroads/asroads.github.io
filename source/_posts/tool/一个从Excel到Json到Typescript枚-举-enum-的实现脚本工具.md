---
title: 一个从Excel到Json到Typescript枚 举(enum)的实现脚本工具
comments: true
categories: tool
tags:
  - ts-node
  - ts
abbrlink: eeb07b93
date: 2024-06-04 11:13:04
---

最近工作中遇到了一个功能埋点的任务，于是就想到这样的功能能不能先把一些信息放到`Excel`里面，生成`json`对象`Typescript` 文件，于是乎就去网上搜索了一下，最终找了一个类似的模板，在这个基础上又追加了一些功能，于是下面的的这个工具产生了。
<!--more-->

## 整体介绍

总来的来讲，脚本工具实现了 `Excel`文件---->`json`文件，然后把`json`文件生成最后我们需要的`Typescript`文件

### 环境

相关依赖

- node

- ts-node
- ejs
- typescript
- yargs

### 最终效果

#### Excel内容

| id         | value      | desc              |
| ---------- | ---------- | ----------------- |
| LevelFund  | LevelFund  | 等级基金界面打开  |
| SeasonBP   | SeasonBP   | 赛季BP界面打开    |
| SeasonInBP | SeasonInBP | 赛季BP2级界面打开 |
| SnakeGift  | SnakeGift  | 毒蛇礼包3界面打开 |

#### 生成的json文件

```bash
 ts-node lib/excel2Json.ts input/TrackData.xlsx -s Sheet1 -key id 
```

-  input/TrackData.xlsx 文件路径
-  -s Sheet1 表名
-  -key id 主键

```json
{
  "LevelFund": {
    "value": "LevelFund",
    "desc": "等级基金界面打开"
  },
  "SeasonBP": {
    "value": "SeasonBP",
    "desc": "赛季BP界面打开"
  },
  "SeasonInBP": {
    "value": "SeasonInBP",
    "desc": "赛季BP2级界面打开"
  },
  "SnakeGift": {
    "value": "SnakeGift",
    "desc": "毒蛇礼包3界面打开"
  }
}
```

#### json文件转换成Tyepscript

```bash
ts-node lib/cli.ts --input ./input/TrackData.json --output  ./output --enumName TrackType --fileName TrackFile --obj
```

- `./input/TrackData.json` // `json`文件路径
- `--output  ./output`  //输出目录 `./output`
- `--enumName TrackType` //生成的枚举名字 `TrackType`
- `--fileName TrackFile` // 生成的`Typescript`文件名字  `TrackFile`
- `--obj` //生成对应的 对象文件 如果不写就不生成

生成的`TrackFile.ts` 内容如下：

```typescript

export enum TrackType {
    /* 等级基金界面打开 */
    LevelFund = "LevelFund",
    /* 赛季BP界面打开 */
    SeasonBP = "SeasonBP",
    /* 赛季BP2级界面打开 */
    SeasonInBP = "SeasonInBP",
    /* 毒蛇礼包3界面打开 */
    SnakeGift = "SnakeGift",
}

interface TrackTypeObjectType {
    value: string;
    desc: string;
}

export const TrackTypeObject: Record<string, TrackTypeObjectType> = {
    LevelFund: {
        value: "LevelFund",
        desc: "等级基金界面打开"
    },
    SeasonBP: {
        value: "SeasonBP",
        desc: "赛季BP界面打开"
    },
    SeasonInBP: {
        value: "SeasonInBP",
        desc: "赛季BP2级界面打开"
    },
    SnakeGift: {
        value: "SnakeGift",
        desc: "毒蛇礼包3界面打开"
    },
};

```

## 具体核心代码和配置

`excel2Json.ts`

```typescript
import * as fs from 'fs';
import * as path from 'path';
import * as XLSX from 'xlsx';
import yargs from 'yargs';

// Define arguments
const argv = yargs
    .option('s', {
        alias: 'sheet',
        description: 'The sheet name to be converted',
        type: 'string',
        default: 'Sheet1'
    })
    .option('key', {
        description: 'The column name to be used as key',
        type: 'string',
        default: null
    })
    .demandCommand(1)
    .help()
    .alias('help', 'h')
    .argv as any;

// Get the file path from the command line arguments
const filePath = argv._[0];
const sheetName = argv.sheet;
const keyColumn = argv.key;

if (!filePath) {
    console.error('Please provide the path to the Excel file.');
    process.exit(1);
}

if (!fs.existsSync(filePath)) {
    console.error('File does not exist:', filePath);
    process.exit(1);
}

// Read the Excel file
const workbook = XLSX.readFile(filePath);
const sheet = workbook.Sheets[sheetName];

if (!sheet) {
    console.error(`Sheet "${sheetName}" does not exist in the file.`);
    process.exit(1);
}

// Convert sheet to JSON
const sheetData:any = XLSX.utils.sheet_to_json(sheet);

// Determine key column if not specified
const keyCol = keyColumn || Object.keys(sheetData[0])[0];

// Transform the data
const jsonData = sheetData.reduce((acc:any, row:any) => {
    const key = row[keyCol];
    const values = Object.keys(row).filter(col => col !== keyCol);

    if (values.length === 1) {
        acc[key] = row[values[0]];
    } else {
        acc[key] = values.reduce((obj:any, col) => {
            obj[col] = row[col];
            return obj;
        }, {});
    }

    return acc;
}, {});

// Write to JSON file
const outputFilePath = path.join(path.dirname(filePath),path.basename(filePath, path.extname(filePath)) + '.json');
fs.writeFileSync(outputFilePath, JSON.stringify(jsonData, null, 2), 'utf8');

console.log(`JSON data has been written to ${outputFilePath}`);

```

`cli.ts`

```typescript
#!/usr/bin/env node
import yargs from 'yargs';
import fs from 'fs';
import * as ejs from 'ejs';
import path from 'path';

const args = yargs.options({
    'input': { type: 'string', demandOption: true, alias: 'i' },
    'output': { type: 'string', demandOption: true, alias: 'o' },
    'enumName': { type: 'string', demandOption: true, alias: 'n' },
    'fileName': { type: 'string', demandOption: false, alias: 'f' },
    'obj': { type: 'boolean', demandOption: false, default: false, alias: 'js' }
}).argv;

const data = JSON.parse(fs.readFileSync(args.input, 'utf-8'));

const enumTemplate = `
export enum <%= enumName %> {
<% for (const key in values) { -%>
<% if (values[key].desc) { -%>
    /* <%= values[key].desc %> */
<% } -%>
    <%= key %> = "<%= values[key].value %>",
<% } -%>
}
`;

const jsTemplate = `
interface <%= enumName %>ObjectType {
    value: string;
    desc: string;
}

export const <%= enumName %>Object: Record<string, <%= enumName %>ObjectType> = {
<% for (const key in values) { -%>
    <%= key %>: {
        value: "<%= values[key].value %>",
        desc: "<%= values[key].desc %>"
    },
<% } -%>
};
`;

const fileName = args.fileName || args.enumName;

const enumOptions = {
    enumName: args.enumName,
    values: data
};

const enumResult = ejs.render(enumTemplate, enumOptions);

let result = enumResult;

if (args.obj) {
    const jsOptions = {
        enumName: args.enumName,
        values: data
    };
    const jsResult = ejs.render(jsTemplate, jsOptions);
    result += jsResult;
}

fs.writeFileSync(path.join(args.output, `${fileName}.ts`), result, { encoding: 'utf-8' });

console.info(`TypeScript enum ${fileName}.ts written to ${args.output}`);
if (args.obj) {
    console.info(`JavaScript object ${fileName}.ts appended to the file`);
}

```

package.json

```json
{
  "name": "json-to-ts-enum",
  "version": "1.0.1",
  "description": "Generate a TypeScript enum and Javascript Object from JSON file",
  "main": "dist/cli.js",
  "keywords": [
    "TypeScript",
    "JSON",
    "enum",
    "Generate"
  ],
  "scripts": {
    "dev": "ts-node lib/cli.ts",
    "build": "tsc",
    "prepublish": "npm run build"
  },
  "bin": {
    "json-to-ts-enum": "dist/cli.js"
  },
  "author": {
    "email": "jules@qusito.nl",
    "name": "Jules Kreutzer | Qusito",
    "url": "https://qusito.nl"
  },
  "license": "MIT",
  "devDependencies": {
    "@types/ejs": "^3.0.5",
    "@types/node": "^14.14.2",
    "@types/yargs": "^15.0.9",
    "ts-node": "^9.0.0",
    "typescript": "^4.0.3"
  },
  "dependencies": {
    "@qusito/json-enum-generator": "^1.0.2",
    "ejs": "^3.1.5",
    "xlsx": "^0.18.5",
    "yargs": "^16.1.0"
  }
}

```

最后仓库地址：[点击前往](https://github.com/jsroads/mylibs/tree/main/json-to-ts-enum)

## 参考

- [json-enum-generator](https://github.com/Qusito/json-enum-generator.git)
- [enum-opt](https://github.com/CXLDILEI/enum-opt/blob/main/README-zh_CN.md)