---
title: 分享一些常见CocosCreator琐碎的组件以及JS代码片段
comments: true
categories: game
tags:
  - Cocos
  - js
  - ts
abbrlink: 54c62aec
date: 2025-03-03 23:31:19
---

平时日常工作中有时候会积攒一些常用的脚本和组件，单独写文章又没有必要，于是攒在一起写一个集合吧，便于日后查阅。
<!--more-->
下面是常用的一些组件和功能

## TS&JS

### 判断组件继承`isChildClassOf`

```typescript
/**
 * Created by guoyuhai on 2025/5/24 20:33
 * Note:
 */
function getSuper(ctor: Function): Function | null {
    const proto = ctor.prototype;
    const superProto = proto && Object.getPrototypeOf(proto);
    return superProto?.constructor || null;
}

/**
 * 判断 subclass 是否是 superclass 的子类（或本身）
 */
export function isChildClassOf(
    subclass: Function | null | undefined,
    superclass: Function | null | undefined
): boolean {
    if (!subclass || !superclass) return false;

    if (typeof subclass !== 'function' || typeof superclass !== 'function') {
        return false;
    }

    if (subclass === superclass) {
        return true;
    }

    let current: Function | null = subclass;

    while (true) {
        const parent = getSuper(current);
        if (!parent) {
            return false;
        }
        if (parent === superclass) {
            return true;
        }
        current = parent;
    }
}
```

### 多字段排序逻辑

下面是将该多字段排序逻辑封装成一个通用的工具函数形式，放入一个 `SortUtils` 工具类中，方便在项目中复用。

```typescript
type SortOrder = 'asc' | 'desc';

interface SortKeyConfig<T> {
  key: keyof T;
  order: SortOrder;
}

type SortKey<T> = keyof T | SortKeyConfig<T>;

interface SortConfig<T> {
  keys: SortKey<T>[];
  defaultOrder?: SortOrder;
}

export class SortUtils {
  /**
   * 多字段排序函数
   * @param data 需要排序的数据数组
   * @param config 排序配置项
   * @returns 排序后的新数组（不会修改原数组）
   */
  static sortBy<T>(data: T[], config: SortConfig<T>): T[] {
    const defaultOrder: SortOrder = config.defaultOrder ?? 'asc';

    return [...data].sort((a, b) => {
      for (const item of config.keys) {
        const key = typeof item === 'string' ? item : item.key;
        const order = typeof item === 'string' ? defaultOrder : item.order;

        const aValue = a[key];
        const bValue = b[key];

        if (aValue !== bValue) {
          if (typeof aValue === 'string' && typeof bValue === 'string') {
            return order === 'asc'
              ? aValue.localeCompare(bValue)
              : bValue.localeCompare(aValue);
          }

          if (typeof aValue === 'number' && typeof bValue === 'number') {
            return order === 'asc' ? aValue - bValue : bValue - aValue;
          }

          if (aValue instanceof Date && bValue instanceof Date) {
            return order === 'asc'
              ? aValue.getTime() - bValue.getTime()
              : bValue.getTime() - aValue.getTime();
          }

          return order === 'asc'
            ? String(aValue).localeCompare(String(bValue))
            : String(bValue).localeCompare(String(aValue));
        }
      }

      return 0;
    });
  }
}
```

#### 使用示例

```typescript
import { SortUtils } from './utils/SortUtils';

interface Person {
  name: string;
  age: number;
  birthday: Date;
}

const people: Person[] = [
  { name: 'Alice', age: 30, birthday: new Date('1994-01-01') },
  { name: 'Bob', age: 25, birthday: new Date('1999-01-01') },
  { name: 'Charlie', age: 25, birthday: new Date('1998-01-01') }
];

// 先按年龄升序，再按生日降序
const sorted = SortUtils.sortBy(people, {
  keys: [
    { key: 'age', order: 'asc' },
    { key: 'birthday', order: 'desc' }
  ]
});
```

### 多字段排序实现（类似 C# orderBy 和 thenBy）

```typescript
enum SortDirection {
  Ascending = 'asc',
  Descending = 'desc'
}

interface SortCondition<T> {
  selector: (item: T) => any;
  direction: SortDirection;
}

class OrderBuilder<T> {
  private readonly source: T[];
  private readonly conditions: SortCondition<T>[] = [];
  private resultCache: T[] | null = null;

  constructor(source: T[]) {
    this.source = source;
  }

  orderBy<K>(selector: (item: T) => K, direction: SortDirection = SortDirection.Ascending): OrderBuilder<T> {
    this.conditions.length = 0;
    this.conditions.push({ selector, direction });
    this.resultCache = null;
    return this;
  }

  orderByDescending<K>(selector: (item: T) => K): OrderBuilder<T> {
    return this.orderBy(selector, SortDirection.Descending);
  }

  thenBy<K>(selector: (item: T) => K, direction: SortDirection = SortDirection.Ascending): OrderBuilder<T> {
    this.conditions.push({ selector, direction });
    this.resultCache = null;
    return this;
  }

  thenByDescending<K>(selector: (item: T) => K): OrderBuilder<T> {
    return this.thenBy(selector, SortDirection.Descending);
  }

  toArray(): T[] {
    if (this.resultCache) {
      return this.resultCache;
    }

    if (this.conditions.length === 0) {
      this.resultCache = [...this.source];
      return this.resultCache;
    }

    this.resultCache = [...this.source].sort((a, b) => {
      for (const condition of this.conditions) {
        const valueA = condition.selector(a);
        const valueB = condition.selector(b);
        
        if (valueA === valueB) {
          continue;
        }
        
        // 针对数值类型的优化
        if (typeof valueA === 'number' && typeof valueB === 'number') {
          return condition.direction === SortDirection.Ascending 
            ? valueA - valueB 
            : valueB - valueA;
        }
        
        const result = valueA < valueB ? -1 : 1;
        return condition.direction === SortDirection.Ascending ? result : -result;
      }
      
      return 0;
    });
    
    return this.resultCache;
  }
}

// 辅助函数
export function orderBy<T, K>(source: T[], selector: (item: T) => K, direction: SortDirection = SortDirection.Ascending): OrderBuilder<T> {
  return new OrderBuilder(source).orderBy(selector, direction);
}

export function orderByDescending<T, K>(source: T[], selector: (item: T) => K): OrderBuilder<T> {
  return orderBy(source, selector, SortDirection.Descending);
}

export { SortDirection, OrderBuilder };
```

#### 使用示例

```typescript
import { orderBy, orderByDescending, SortDirection } from './OrderBuilder';

// 定义数据接口
interface Person {
  id: number;
  name: string;
  age: number;
  salary: number;
  department: string;
}

// 创建示例数据
const people: Person[] = [
  { id: 1, name: "张三", age: 30, salary: 10000, department: "技术部" },
  { id: 2, name: "李四", age: 25, salary: 8000, department: "市场部" },
  { id: 3, name: "王五", age: 30, salary: 12000, department: "技术部" },
  { id: 4, name: "赵六", age: 25, salary: 9000, department: "人事部" },
  { id: 5, name: "钱七", age: 35, salary: 15000, department: "技术部" },
  { id: 6, name: "孙八", age: 28, salary: 11000, department: "市场部" },
  { id: 7, name: "周九", age: 35, salary: 14000, department: "人事部" },
  { id: 8, name: "吴十", age: 28, salary: 13000, department: "技术部" },
];

// 示例1: 按年龄升序，然后按薪资降序排序
console.log("示例1: 按年龄升序，然后按薪资降序排序");
const result1 = orderBy(people, p => p.age)
  .thenByDescending(p => p.salary)
  .toArray();

console.table(result1);

// 示例2: 按部门升序，然后按年龄降序，最后按姓名升序排序
console.log("\n示例2: 按部门升序，然后按年龄降序，最后按姓名升序排序");
const result2 = orderBy(people, p => p.department)
  .thenByDescending(p => p.age)
  .thenBy(p => p.name)
  .toArray();

console.table(result2);

// 示例3: 直接使用降序排序
console.log("\n示例3: 按薪资降序排序");
const result3 = orderByDescending(people, p => p.salary).toArray();

console.table(result3);

// 示例4: 性能测试 - 生成大量数据并排序
console.log("\n示例4: 性能测试");
const generateLargeDataset = (size: number): Person[] => {
  const departments = ["技术部", "市场部", "人事部", "财务部", "行政部"];
  const names = ["张", "李", "王", "赵", "钱", "孙", "周", "吴", "郑", "陈"];
  
  return Array.from({ length: size }, (_, i) => ({
    id: i + 1,
    name: names[Math.floor(Math.random() * names.length)] + (i + 1),
    age: 20 + Math.floor(Math.random() * 40),
    salary: 5000 + Math.floor(Math.random() * 15000),
    department: departments[Math.floor(Math.random() * departments.length)]
  }));
};

const largeDataset = generateLargeDataset(10000);

console.time("排序10000条数据");
const result4 = orderBy(largeDataset, p => p.department)
  .thenBy(p => p.age)
  .thenByDescending(p => p.salary)
  .toArray();
console.timeEnd("排序10000条数据");

console.log(`排序后的前5条数据:`);
console.table(result4.slice(0, 5));
```

### 判断是否是合法的枚举值

```typescript
    /**
     * 判断是否是合法的枚举值
     * @param enumObj
     * @param val
     * @private
     */
    private isEnumValue<T extends Record<string, string | number>>(enumObj: T, val: unknown): val is T[keyof T] {
        return typeof val === "number" && Object.values(enumObj).includes(val as number);
    }
```

### 单例继承基类

```typescript
/** 基础基类 */
export class Base<T extends Base<T>> {
    private static _instances: Map<new (...args: any[]) => any, any> = new Map();

    public constructor() {
        // 防止直接实例化
        if (new.target === Base) {
            throw new Error("Base class cannot be instantiated directly.");
        }
    }

    /**
     * 获取一个单例
     * @param args 实例化参数
     * @returns {T} 单例
     */
    public static getInstance<T extends Base<T>>(this: new (...args: any[]) => T, ...args: ConstructorParameters<typeof this>): T {
        if (!Base._instances.has(this)) {
            Base._instances.set(this, new this(...args));
        }
        return Base._instances.get(this) as T;
    }
}
```

### TypeScript 版本对比工具函数

一个优雅的 TypeScript 版本对比工具函数，支持语义化版本号(Semantic Versioning)的比较，包括主版本号、次版本号、修订号以及预发布标识符的处理。完全符合语义化版本规范(`SemVer`)，可以处理各种复杂的版本比较场景，包括预发布版本和构建元数据。函数设计简洁明了，易于使用和扩展。

```typescript
/**
 * 比较两个版本号
 * @param version1 第一个版本号，如 "1.2.3" 或 "2.0.0-beta.1"
 * @param version2 第二个版本号，如 "1.2.4" 或 "2.0.0-alpha.5"
 * @returns 如果 version1 > version2 返回 1，如果 version1 < version2 返回 -1，如果相等返回 0
 */
export function compareVersions(version1: string, version2: string): number {
  // 处理空值情况
  if (!version1 && !version2) return 0;
  if (!version1) return -1;
  if (!version2) return 1;

  // 将版本号分割为主版本号部分和预发布标识符部分
  const [v1, prerelease1] = version1.split('-');
  const [v2, prerelease2] = version2.split('-');

  // 比较主版本号部分 (x.y.z)
  const v1Parts = v1.split('.').map(part => parseInt(part, 10));
  const v2Parts = v2.split('.').map(part => parseInt(part, 10));

  // 确保版本号有三个部分 (补充缺失的部分为0)
  while (v1Parts.length < 3) v1Parts.push(0);
  while (v2Parts.length < 3) v2Parts.push(0);

  // 比较主版本号、次版本号和修订号
  for (let i = 0; i < 3; i++) {
    if (v1Parts[i] > v2Parts[i]) return 1;
    if (v1Parts[i] < v2Parts[i]) return -1;
  }

  // 如果主版本号部分相同，比较预发布标识符
  // 预发布版本号小于相应的正式版本号
  if (prerelease1 && !prerelease2) return -1;
  if (!prerelease1 && prerelease2) return 1;
  if (!prerelease1 && !prerelease2) return 0;

  // 比较预发布标识符
  const pr1Parts = prerelease1.split('.');
  const pr2Parts = prerelease2.split('.');

  const minLength = Math.min(pr1Parts.length, pr2Parts.length);

  for (let i = 0; i < minLength; i++) {
    // 如果两部分都是数字，进行数值比较
    const isNum1 = /^\d+$/.test(pr1Parts[i]);
    const isNum2 = /^\d+$/.test(pr2Parts[i]);

    if (isNum1 && isNum2) {
      const num1 = parseInt(pr1Parts[i], 10);
      const num2 = parseInt(pr2Parts[i], 10);
      if (num1 > num2) return 1;
      if (num1 < num2) return -1;
      continue;
    }

    // 如果一个是数字一个是字符串，数字优先级较低
    if (isNum1 && !isNum2) return -1;
    if (!isNum1 && isNum2) return 1;

    // 两个都是字符串，按字典序比较
    const comp = pr1Parts[i].localeCompare(pr2Parts[i]);
    if (comp !== 0) return comp;
  }

  // 如果前面部分都相同，较长的预发布标识符版本较大
  return pr1Parts.length - pr2Parts.length;
}

/**
 * 检查版本是否在指定范围内
 * @param version 要检查的版本号
 * @param minVersion 最小版本要求（包含）
 * @param maxVersion 最大版本要求（不包含），可选
 * @returns 如果版本在范围内返回 true，否则返回 false
 */
export function isVersionInRange(
  version: string, 
  minVersion: string, 
  maxVersion?: string
): boolean {
  const minCheck = compareVersions(version, minVersion) >= 0;
  
  if (!maxVersion) return minCheck;
  
  const maxCheck = compareVersions(version, maxVersion) < 0;
  return minCheck && maxCheck;
}

/**
 * 获取两个版本之间的差异类型
 * @param version1 第一个版本号
 * @param version2 第二个版本号
 * @returns 'major' | 'minor' | 'patch' | 'prerelease' | 'same'
 */
export function getVersionDifferenceType(
  version1: string, 
  version2: string
): 'major' | 'minor' | 'patch' | 'prerelease' | 'same' {
  if (compareVersions(version1, version2) === 0) return 'same';

  const [v1] = version1.split('-');
  const [v2] = version2.split('-');
  
  const v1Parts = v1.split('.').map(part => parseInt(part, 10));
  const v2Parts = v2.split('.').map(part => parseInt(part, 10));

  // 确保版本号有三个部分
  while (v1Parts.length < 3) v1Parts.push(0);
  while (v2Parts.length < 3) v2Parts.push(0);

  if (v1Parts[0] !== v2Parts[0]) return 'major';
  if (v1Parts[1] !== v2Parts[1]) return 'minor';
  if (v1Parts[2] !== v2Parts[2]) return 'patch';
  
  return 'prerelease';
}

/**
 * 解析版本号为结构化对象
 * @param version 版本号字符串
 * @returns 解析后的版本对象
 */
export function parseVersion(version: string): {
  major: number;
  minor: number;
  patch: number;
  prerelease?: string;
  build?: string;
} {
  // 处理构建元数据
  const [versionPart, buildMetadata] = version.split('+');
  
  // 处理预发布版本
  const [mainVersion, prerelease] = versionPart.split('-');
  
  // 解析主版本号
  const [majorStr, minorStr, patchStr] = mainVersion.split('.');
  
  return {
    major: parseInt(majorStr || '0', 10),
    minor: parseInt(minorStr || '0', 10),
    patch: parseInt(patchStr || '0', 10),
    prerelease: prerelease,
    build: buildMetadata
  };
}

/**
 * 获取下一个版本号
 * @param currentVersion 当前版本号
 * @param type 要增加的版本类型: 'major', 'minor', 'patch'
 * @param prerelease 可选的预发布标识符
 * @returns 新的版本号
 */
export function getNextVersion(
  currentVersion: string,
  type: 'major' | 'minor' | 'patch',
  prerelease?: string
): string {
  const parsed = parseVersion(currentVersion);
  
  switch (type) {
    case 'major':
      parsed.major += 1;
      parsed.minor = 0;
      parsed.patch = 0;
      break;
    case 'minor':
      parsed.minor += 1;
      parsed.patch = 0;
      break;
    case 'patch':
      parsed.patch += 1;
      break;
  }
  
  let version = `${parsed.major}.${parsed.minor}.${parsed.patch}`;
  
  if (prerelease) {
    version += `-${prerelease}`;
  }
  
  if (parsed.build) {
    version += `+${parsed.build}`;
  }
  
  return version;
}
```

#### 使用示例

```typescript
// 基本版本比较
console.log(compareVersions("1.2.3", "1.2.4"));  // 输出: -1
console.log(compareVersions("2.0.0", "1.9.9"));  // 输出: 1
console.log(compareVersions("1.2.3", "1.2.3"));  // 输出: 0

// 带预发布标识符的比较
console.log(compareVersions("1.0.0-alpha", "1.0.0-beta"));  // 输出: -1
console.log(compareVersions("1.0.0", "1.0.0-beta"));  // 输出: 1
console.log(compareVersions("1.0.0-beta.11", "1.0.0-beta.2"));  // 输出: 1

// 版本范围检查
console.log(isVersionInRange("1.2.3", "1.0.0", "2.0.0"));  // 输出: true
console.log(isVersionInRange("2.0.0", "1.0.0", "2.0.0"));  // 输出: false
console.log(isVersionInRange("0.9.9", "1.0.0"));  // 输出: false

// 版本差异类型
console.log(getVersionDifferenceType("1.2.3", "2.0.0"));  // 输出: 'major'
console.log(getVersionDifferenceType("1.2.3", "1.3.0"));  // 输出: 'minor'
console.log(getVersionDifferenceType("1.2.3", "1.2.4"));  // 输出: 'patch'
console.log(getVersionDifferenceType("1.2.3-alpha", "1.2.3-beta"));  // 输出: 'prerelease'

// 获取下一个版本
console.log(getNextVersion("1.2.3", "major"));  // 输出: "2.0.0"
console.log(getNextVersion("1.2.3", "minor"));  // 输出: "1.3.0"
console.log(getNextVersion("1.2.3", "patch"));  // 输出: "1.2.4"
console.log(getNextVersion("1.2.3", "major", "alpha.1"));  // 输出: "2.0.0-alpha.1"
```

### 一个可取消的 Timer

```typescript
type TimerCallback = () => void;

class CancellableTimer {
  private timerId: NodeJS.Timeout | null = null;
  private isCancelled = false;

  constructor(private callback: TimerCallback, private delay: number) {}

  start() {
    if (this.isCancelled) {
      console.warn("Timer has been cancelled and cannot be started.");
      return;
    }
    if (this.timerId !== null) {
      console.warn("Timer is already running.");
      return;
    }

    this.timerId = setTimeout(() => {
      if (!this.isCancelled) {
        this.callback();
      }
      this.clear();
    }, this.delay);
  }

  cancel() {
    if (this.timerId !== null) {
      clearTimeout(this.timerId);
      this.isCancelled = true;
      this.clear();
      console.log("Timer cancelled.");
    } else {
      console.warn("No active timer to cancel.");
    }
  }

  private clear() {
    this.timerId = null;
  }
}

// 使用示例
const timer = new CancellableTimer(() => {
  console.log("Timer executed!");
}, 5000);

timer.start();

// 例如3秒后取消它
setTimeout(() => {
  timer.cancel();
}, 3000);

```



## Cocos

### 计算config.json和index.js文件连接后的MD5值

```javascript
/**
 * MD5计算器 - 计算config.json和index.js文件连接后的MD5值
 */

const fs = require('fs');
const path = require('path');
const crypto = require('crypto');

/**
 * 计算指定文件的MD5值
 * @param {string} configPath - config.json文件路径
 * @param {string} indexPath - index.js文件路径
 * @returns {string} - MD5哈希值
 */
function calculateMD5(configPath, indexPath) {
  try {
    // 检查文件是否存在
    if (!fs.existsSync(configPath)) {
      throw new Error(`配置文件不存在: ${configPath}`);
    }
    if (!fs.existsSync(indexPath)) {
      throw new Error(`索引文件不存在: ${indexPath}`);
    }

    // 读取文件内容
    const configContent = fs.readFileSync(configPath);
    const indexContent = fs.readFileSync(indexPath);

    // 创建一个Buffer来存储连接后的内容
    const combinedContent = Buffer.concat([configContent, indexContent]);

    // 计算MD5哈希值
    const md5Hash = crypto.createHash('md5').update(combinedContent).digest('hex');

    return md5Hash;
  } catch (error) {
    console.error('计算MD5时出错:', error.message);
    throw error;
  }
}

/**
 * 处理指定目录下的所有模块
 * @param {string} baseDir - 基础目录
 */
function processModules(baseDir) {
  try {
    // 获取assets目录下的所有模块目录
    const assetsDir = path.join(baseDir, 'assets');
    const moduleDirs = fs.readdirSync(assetsDir)
      .filter(item => {
        const itemPath = path.join(assetsDir, item);
        return fs.statSync(itemPath).isDirectory();
      });

    console.log('找到以下模块:');
    moduleDirs.forEach(module => console.log(`- ${module}`));
    console.log('\n计算MD5值:');

    // 处理每个模块
    moduleDirs.forEach(module => {
      const moduleDir = path.join(assetsDir, module);
      
      // 查找config.json文件
      const configFiles = fs.readdirSync(moduleDir)
        .filter(file => file.includes('config') && file.endsWith('.json'));
      
      // 查找index.js文件
      const indexFiles = fs.readdirSync(moduleDir)
        .filter(file => file.includes('index') && file.endsWith('.js'));
      
      if (configFiles.length > 0 && indexFiles.length > 0) {
        const configPath = path.join(moduleDir, configFiles[0]);
        const indexPath = path.join(moduleDir, indexFiles[0]);
        
        const md5Value = calculateMD5(configPath, indexPath);
        console.log(`模块 ${module}:`);
        console.log(`  配置文件: ${configFiles[0]}`);
        console.log(`  索引文件: ${indexFiles[0]}`);
        console.log(`  MD5值: ${md5Value}\n`);
      } else {
        console.log(`模块 ${module}: 缺少必要的文件 (config.json 或 index.js)\n`);
      }
    });
  } catch (error) {
    console.error('处理模块时出错:', error.message);
  }
}

// 获取当前脚本所在目录作为基础目录
const baseDir = __dirname;
console.log(`基础目录: ${baseDir}\n`);

// 处理模块
processModules(baseDir);
```

参考地址:https://forum.cocos.org/t/topic/127384

### 阻止多点触摸

不要用 cc.macro.ENABLE_MULTI_TOUCH

```javascript
使用
//@ts-ignore
cc.internal.inputManager._maxTouches = 1
```

参考地址:https://forum.cocos.org/t/bug-cc-macro-enable-multi-touch-false-node--touchlistener-setswallowtouches-false/92851/28

### 如何在浏览器控制台获取类对象？

如果是 Cocos 本地唤起的 比如 http://localhost:7456/ 使用 

```javascript
__quick_compile_project__.require("ComboDragController").ComboDragController.ins().isOpen()
```

其中替换自己的 比如 ComboDragController
如果是构建后的包 比如

```bash
http://192.168.1.74/games/test/blockpuzzle/1android_branch/android_v1.81.01_4.2.7_DXM5/
```

可以这样测试：

```javascript
__require("ComboDragController").ComboDragController.ins().isOpen()
```

### 场景自动释放

待补充

### 滑动组件

```typescript
/**
 * Created by jsroads
 * Note:
 */
const { ccclass, property } = cc._decorator;

type SwipeDirection = 'left' | 'right' | 'up' | 'down';

@ccclass
export default class SwipeDetector extends cc.Component {
    @property({ tooltip: "滑动判定的最小距离（像素）" })
    minSwipeDistance: number = 50;

    @property({ type: cc.Component.EventHandler, tooltip: "滑动时触发的回调事件" })
    onSwipeEvents: cc.Component.EventHandler[] = [];

    private startTouchPos: cc.Vec2 | null = null;

    onEnable() {
        this.node.on(cc.Node.EventType.TOUCH_START, this.onTouchStart, this);
        this.node.on(cc.Node.EventType.TOUCH_END, this.onTouchEnd, this);
        this.node.on(cc.Node.EventType.TOUCH_CANCEL, this.onTouchEnd, this);
    }

    onDisable() {
        this.node.off(cc.Node.EventType.TOUCH_START, this.onTouchStart, this);
        this.node.off(cc.Node.EventType.TOUCH_END, this.onTouchEnd, this);
        this.node.off(cc.Node.EventType.TOUCH_CANCEL, this.onTouchEnd, this);
    }

    private onTouchStart(event: cc.Event.EventTouch) {
        this.startTouchPos = event.getLocation();
    }

    private onTouchEnd(event: cc.Event.EventTouch) {
        if (!this.startTouchPos) return;

        const endPos = event.getLocation();
        const delta = endPos.sub(this.startTouchPos);
        const absDx = Math.abs(delta.x);
        const absDy = Math.abs(delta.y);

        let direction: SwipeDirection | null = null;

        if (absDx < this.minSwipeDistance && absDy < this.minSwipeDistance) {
            return; // 滑动距离不足
        }

        if (absDx > absDy) {
            direction = delta.x > 0 ? 'right' : 'left';
        } else {
            direction = delta.y > 0 ? 'up' : 'down';
        }

        // 派发事件
        this.emitSwipeEvent(direction, delta.mag());
    }

    private emitSwipeEvent(direction: SwipeDirection, distance: number) {
        for (let handler of this.onSwipeEvents) {
            if (handler && handler.target) {
                handler.emit([
                    direction,
                    distance
                ]);
            }
        }
    }
}

```

### 倒计时组件

```typescript
const { ccclass, property } = cc._decorator;

@ccclass
export default class CountdownTimer extends cc.Component {
    @property({ tooltip: "倒计时的总秒数" })
    totalSeconds: number = 10;

    @property({ type: cc.Component.EventHandler, tooltip: "每秒更新时触发的事件，参数为剩余秒数（number）" })
    onUpdateEvents: cc.Component.EventHandler[] = [];

    @property({ type: cc.Component.EventHandler, tooltip: "倒计时结束时触发的事件" })
    onCompleteEvents: cc.Component.EventHandler[] = [];

    private remainingSeconds: number = 0;
    private timerCallback: number = 0;

    onEnable() {
        // this.startTimer();
    }

    onDisable() {
        this.clearTimer();
    }

    public startTimer() {
        this.clearTimer();

        this.remainingSeconds = this.totalSeconds;
        this.emitUpdate(this.remainingSeconds);

         this.schedule(() => {
            this.remainingSeconds--;

            if (this.remainingSeconds > 0) {
                this.emitUpdate(this.remainingSeconds);
            } else {
                this.emitUpdate(0);
                this.emitComplete();
                this.clearTimer();
            }
        }, 1, this.totalSeconds - 1);

         this.timerCallback = 168;
    }

    private emitUpdate(seconds: number) {
        for (const handler of this.onUpdateEvents) {
            if (handler && handler.target) {
                handler.emit([seconds]);
            }
        }
    }

    private emitComplete() {
        for (const handler of this.onCompleteEvents) {
            if (handler && handler.target) {
                handler.emit([]);
            }
        }
    }

    public clearTimer() {
        if (this.timerCallback !== undefined) {
            this.unscheduleAllCallbacks();
            this.timerCallback = undefined;
            this.remainingSeconds = 0;
            console.log("Timer cleared");
        }
        console.log("Timer cleared ~~");
    }
}
```

## 总结

上面是这段时间积累的一些脚本，虽然有些零碎，但都是工作中常见的一些工具函数或技巧，希望能帮到需要的人。