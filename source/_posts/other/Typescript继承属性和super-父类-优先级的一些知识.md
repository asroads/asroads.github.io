---
title: Typescript继承属性和super(父类)优先级的一些知识
comments: true
categories: other
tags:
  - ts
abbrlink: 662a6885
date: 2021-03-05 12:01:49
---

做游戏的时候有时候为了对应多个平台，需要对父类抽象化，但是在自对象调用具体的API的时候我们希望能够具体化，比如一些子类的属性，在父类里可能用any 类型但是子类里面我们希望是具体的类型，比如和平台SDK对应的类型，下面就是这几天遇到的一个坑，在填坑后顺便分析了一下结果，作为后面的一个避免入坑法则。
<!--more-->

下面用几次测试 结果对比：

## 测试一  普通函数

方法调用

```typescript
let dog = new Dog();
dog.run()
dog.test()
```

完整代码：

```typescript
class Animal {
    protected info:any = {age:18,name:"unknown"}
    constructor() {
        //this.run();
    }
    run() {
        console.log("                                          ")
        console.log("------------Animal run ------------------------------")
        console.log(`Animal pre run ! age:${this.info.age} name:${this.info.name}`);
        this.info.name = "Animal类";
        console.log(`Animal after run ! age:${this.info.age} name:${this.info.name}`);
        console.log("                                          ")
    }
}
class Dog extends Animal {
    protected info:any = {age:20,name:"Dog"}
    constructor() {
        super();
        console.log(`------重点是这个------ Dog2 run ! age:${this.info.age} name:${this.info.name}`);
        //console.log("----本来我以为这个应该是 name 是 哈士奇  结果是 子类默认值 Dog---")
        //this.run();
        console.log(`Dog3 run ! age:${this.info.age} name:${this.info.name}`);
    }
    run() {
        console.log("                                          ")
        console.log("--------------Dog run----------------------------")
        super.run();
        console.log(`Dog pre run ! age:${this.info.age} name:${this.info.name}`);
        this.info.name = "哈士奇";
        console.log(`Dog1 after run  ! age:${this.info.age} name:${this.info.name}`);
        console.log("                                          ")
    }
    test() {
        console.log("                                          ")
        console.log("--------------Dog test----------------------------")
        console.log(`Dog3 pre run ! age:${this.info.age} name:${this.info.name}`);
       this.info.name = "金毛";
       console.log(`Dog4 after run  ! age:${this.info.age} name:${this.info.name}`);  
    }
}
let dog = new Dog();
dog.run()
dog.test()
```

结果是：

```bash
"------重点是这个------ Dog2 run ! age:20 name:Dog"
"Dog3 run ! age:20 name:Dog"
"                                          "
"--------------Dog run----------------------------"
"                                          "
"------------Animal run ------------------------------"
"Animal pre run ! age:20 name:Dog"
"Animal after run ! age:20 name:Animal类"
"                                          "
"Dog pre run ! age:20 name:Animal类"
"Dog1 after run  ! age:20 name:哈士奇"
"                                          "
"                                          "
"--------------Dog test----------------------------"
"Dog3 pre run ! age:20 name:哈士奇"
"Dog4 after run  ! age:20 name:金毛"
```

## 测试二  构造函数

方法调用

```typescript
let dog = new Dog();
```

完整代码：

```typescript
class Animal {
    protected info:any = {age:18,name:"unknown"}
    constructor() {
        this.run();
    }
    run() {
        console.log("------------Animal run ------------------------------")
        console.log(`Animal pre run ! age:${this.info.age} name:${this.info.name}`);
        this.info.name = "Animal类";
        console.log(`Animal after run ! age:${this.info.age} name:${this.info.name}`);
        console.log("                                          ")
    }
}
class Dog extends Animal {
    protected info:any = {age:20,name:"Dog"}
    constructor() {
        super();
        console.log(`------重点是这个------ Dog2 run ! age:${this.info.age} name:${this.info.name}`);
        console.log("----本来我以为这个应该是 name 是 哈士奇  结果是 子类默认值 Dog---")
        this.run();
        console.log(`Dog3 run ! age:${this.info.age} name:${this.info.name}`);
    }
    run() {
        console.log("--------------Dog run----------------------------")
        super.run();
        console.log(`Dog pre run ! age:${this.info.age} name:${this.info.name}`);
        this.info.name = "哈士奇";
        console.log(`Dog1 after run  ! age:${this.info.age} name:${this.info.name}`);
        console.log("                                          ")
    }
    test() {
        console.log("                                          ")
        console.log("--------------Dog test----------------------------")
        console.log(`Dog3 pre run ! age:${this.info.age} name:${this.info.name}`);
       this.info.name = "金毛";
       console.log(`Dog4 after run  ! age:${this.info.age} name:${this.info.name}`);  
    }
}
let dog = new Dog();
//dog.test()
```

输出结果：

```bash
"--------------Dog run----------------------------"
"------------Animal run ------------------------------"
"Animal pre run ! age:18 name:unknown"
"Animal after run ! age:18 name:Animal类"
"                                          "
"Dog pre run ! age:18 name:Animal类"
"Dog1 after run  ! age:18 name:哈士奇"
"                                          "
"------重点是这个------ Dog2 run ! age:20 name:Dog"
"----本来我以为这个应该是 name 是 哈士奇  结果是 子类默认值 Dog---"
"--------------Dog run----------------------------"
"------------Animal run ------------------------------"
"Animal pre run ! age:20 name:Dog"
"Animal after run ! age:20 name:Animal类"
"                                          "
"Dog pre run ! age:20 name:Animal类"
"Dog1 after run  ! age:20 name:哈士奇"
"                                          "
"Dog3 run ! age:20 name:哈士奇"
```



## 测试三  去掉子类的复写属性

方法调用：

```typescript
let dog = new Dog();
//dog.test()
```

完整代码：

```typescript
class Animal {
    protected info:any = {age:18,name:"unknown"}
    constructor() {
        this.run();
    }
    run() {
        console.log("------------Animal run ------------------------------")
        console.log(`Animal pre run ! age:${this.info.age} name:${this.info.name}`);
        this.info.name = "Animal类";
        console.log(`Animal after run ! age:${this.info.age} name:${this.info.name}`);
        console.log("                                          ")
    }
}
class Dog extends Animal {
    constructor() {
        super();
        console.log(`------重点是这个------ Dog2 run ! age:${this.info.age} name:${this.info.name}`);
        console.log("----一些如我所愿 当下 name 是 哈士奇 子类默认值 Dog---")
        this.run();
        console.log(`Dog3 run ! age:${this.info.age} name:${this.info.name}`);
    }
    run() {
        console.log("--------------Dog run----------------------------")
        super.run();
        console.log(`Dog pre run ! age:${this.info.age} name:${this.info.name}`);
        this.info.name = "哈士奇";
        console.log(`Dog1 after run  ! age:${this.info.age} name:${this.info.name}`);
        console.log("                                          ")
    }
    test() {
        console.log("                                          ")
        console.log("--------------Dog test----------------------------")
        console.log(`Dog3 pre run ! age:${this.info.age} name:${this.info.name}`);
       this.info.name = "金毛";
       console.log(`Dog4 after run  ! age:${this.info.age} name:${this.info.name}`);  
    }
}
let dog = new Dog();
//dog.test()
```

输出结果：

```bash
"--------------Dog run----------------------------"
"------------Animal run ------------------------------"
"Animal pre run ! age:18 name:unknown"
"Animal after run ! age:18 name:Animal类"
"                                          "
"Dog pre run ! age:18 name:Animal类"
"Dog1 after run  ! age:18 name:哈士奇"
"                                          "
"------重点是这个------ Dog2 run ! age:18 name:哈士奇"
"----一些如我所愿 当下 name 是 哈士奇 子类默认值 Dog---"
"--------------Dog run----------------------------"
"------------Animal run ------------------------------"
"Animal pre run ! age:18 name:哈士奇"
"Animal after run ! age:18 name:Animal类"
"                                          "
"Dog pre run ! age:18 name:Animal类"
"Dog1 after run  ! age:18 name:哈士奇"
"                                          "
"Dog3 run ! age:18 name:哈士奇"
```

去掉了Dog 类里面的 info 属性。

## 总结

Typescript 函数里面 constructor里面 super 初始化优先级 高于自身属性默认值 初始化的优先级 ，一句话就是 constructor函数里面的super类的调用 比自身属性默认值初始化调用要早。核心知识：**注意父类已经定义的属性（public 或者 protected）子类不要重复定义即可避免这样的代码调用和自己臆想不一致的问题。**

