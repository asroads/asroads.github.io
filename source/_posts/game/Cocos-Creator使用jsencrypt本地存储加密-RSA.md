---
title: Cocos Creator使用jsencrypt本地存储加密(RSA)
comments: true
categories: game
tags:
  - Cocos
  - js
abbrlink: 8be86f95
date: 2020-09-04 16:10:45
---

前面介绍过前端加密的一种方式是AES（对称加密）后面又看到关于加密解密的文章，觉得甚是不错，于是再次尝试一下用非对称加密写一下。这里使用[jsencrypt](https://github.com/travist/jsencrypt)(RSA) 非对称加密的一种思路。 
<!--more-->
关于对称加密和非对称加密的介绍，这里不再赘述，直接说步骤吧！

### 步骤

首先我们 生成私钥和公钥

私钥生成:

```shell
openssl genrsa -out rsa_1024_priv.pem 1024
```

然后生成公钥:

```shell
openssl rsa -pubout -in rsa_1024_priv.pem -out rsa_1024_pub.pem
```

此时我们的本地生成了两个文件

![image-20200904161632411](Cocos-Creator使用jsencrypt本地存储加密-RSA/image-20200904161632411.png)

对应的就是我们的私钥和公钥：内容如下：

```text
-----BEGIN RSA PRIVATE KEY-----
MIICWwIBAAKBgQC8nNKt9lJdwwFKzRp0D2wPtUcXwMj2t8smhUmLvTTiomej11bg
xf75I4zxIq4sjYE8yyzoWXjrJ2dbakDPFlQxH1kC1mVCBrA1HGAJ8EULyVk52Uu3
MbpW5rCPGUh5gqpSCgVuzvO8ij5UxOxgPZMXH/xH/43PcCfTyWQOIliHHwIDAQAB
AoGAZ9/CU5ZN5F9qDcUJZZO8wqXmYlmFFANCuZM8Xt40CwDALUaCDUZd1t3SWFbu
L5E69L08nTXxVyibTogk6yfz8yrxp18ckT8XbtgNor0MFMPxku7Rg14r14ha4fg5
172FjyH5PI0jJfVwKKLl2cUjzHzEuTyHsXLChjWDrplE8/ECQQDt3okMMaxGGPpY
1jKxKDojdio3Bh32A0FoVCXHZQ/K/7pp4qRYfDY28dzv4e+/ZmmpCWEfSLRoebWR
l2bFMkInAkEAyv0oetOYgbj4a+kl6GW9wKcZedNcXX1SiGFRO2Quybj1J4NpheGz
/A570zUXOR13WYiWiMRHiLZcLidtLMtGSQJAMOH2kAb1PEKN0AvUxwtZR0hVTJF5
PsFUg7gnO17NbA45pODqnTSJf6kcmj94IYFOXWqMBv3N9VptscnHWOcHKQJAXpko
WkVuF1Zqf2/CONHhYnz9RGcZ7n/PDLu9rZ38qAnIRkfZz4jl5huh3UYqsnVm/5TG
AqrmY3II94FsQanh8QJAWX+12fOhCY490btr0nvlReroyci1obEm/73NHZIKSW8l
U5bkherW3VXhwjdQHiDlN4EJTe3HooCA2Qa7Zu21CA==
-----END RSA PRIVATE KEY-----
```

```text
-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC8nNKt9lJdwwFKzRp0D2wPtUcX
wMj2t8smhUmLvTTiomej11bgxf75I4zxIq4sjYE8yyzoWXjrJ2dbakDPFlQxH1kC
1mVCBrA1HGAJ8EULyVk52Uu3MbpW5rCPGUh5gqpSCgVuzvO8ij5UxOxgPZMXH/xH
/43PcCfTyWQOIliHHwIDAQAB
-----END PUBLIC KEY-----
```

这里我们把下载好的 文件 jsencrypt.js 或者 jsencrypt.min.js 任意一个 复制到项目

![image-20200904170304784](Cocos-Creator使用jsencrypt本地存储加密-RSA/image-20200904170304784.png)

assets 下的 libs（自己新建）下 顺便把 声明文件 拷贝过去 (文件名字保持一致)可以把

 改名 为 `JSEncrypt.d.ts`修改为 jsencrypt.d.ts

![image-20200904171618152](Cocos-Creator使用jsencrypt本地存储加密-RSA/image-20200904171618152.png)

效果如下:

![image-20200904172019485](Cocos-Creator使用jsencrypt本地存储加密-RSA/image-20200904172019485.png)

**注意：我这里没有使用导入为插件 这个功能 大家自己使用的时候特别注意！！**

### 编写代码

```typescript
import { JSEncrypt } from "../libs/jsencrypt";

const {ccclass, property} = cc._decorator;
@ccclass
export default class Helloworld extends cc.Component {

    @property(cc.Label)
    label: cc.Label = null;

    @property
    text: string = 'hello';

    start() {
        // init logic
        const PRIVATE_KEY = `-----BEGIN RSA PRIVATE KEY-----
MIICWwIBAAKBgQC8nNKt9lJdwwFKzRp0D2wPtUcXwMj2t8smhUmLvTTiomej11bg
xf75I4zxIq4sjYE8yyzoWXjrJ2dbakDPFlQxH1kC1mVCBrA1HGAJ8EULyVk52Uu3
MbpW5rCPGUh5gqpSCgVuzvO8ij5UxOxgPZMXH/xH/43PcCfTyWQOIliHHwIDAQAB
AoGAZ9/CU5ZN5F9qDcUJZZO8wqXmYlmFFANCuZM8Xt40CwDALUaCDUZd1t3SWFbu
L5E69L08nTXxVyibTogk6yfz8yrxp18ckT8XbtgNor0MFMPxku7Rg14r14ha4fg5
172FjyH5PI0jJfVwKKLl2cUjzHzEuTyHsXLChjWDrplE8/ECQQDt3okMMaxGGPpY
1jKxKDojdio3Bh32A0FoVCXHZQ/K/7pp4qRYfDY28dzv4e+/ZmmpCWEfSLRoebWR
l2bFMkInAkEAyv0oetOYgbj4a+kl6GW9wKcZedNcXX1SiGFRO2Quybj1J4NpheGz
/A570zUXOR13WYiWiMRHiLZcLidtLMtGSQJAMOH2kAb1PEKN0AvUxwtZR0hVTJF5
PsFUg7gnO17NbA45pODqnTSJf6kcmj94IYFOXWqMBv3N9VptscnHWOcHKQJAXpko
WkVuF1Zqf2/CONHhYnz9RGcZ7n/PDLu9rZ38qAnIRkfZz4jl5huh3UYqsnVm/5TG
AqrmY3II94FsQanh8QJAWX+12fOhCY490btr0nvlReroyci1obEm/73NHZIKSW8l
U5bkherW3VXhwjdQHiDlN4EJTe3HooCA2Qa7Zu21CA==
-----END RSA PRIVATE KEY-----`;

        const PUBLIC_KEY  = `-----BEGIN PUBLIC KEY-----
MIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQC8nNKt9lJdwwFKzRp0D2wPtUcX
wMj2t8smhUmLvTTiomej11bgxf75I4zxIq4sjYE8yyzoWXjrJ2dbakDPFlQxH1kC
1mVCBrA1HGAJ8EULyVk52Uu3MbpW5rCPGUh5gqpSCgVuzvO8ij5UxOxgPZMXH/xH
/43PcCfTyWQOIliHHwIDAQAB
-----END PUBLIC KEY-----`;

        const encryptor = new JSEncrypt({default_key_size:"1024"}); // RSA加密器
        encryptor.setPublicKey(PUBLIC_KEY);

        const decryptor = new JSEncrypt({default_key_size:"1024"}); // RSA解密器
        decryptor.setPrivateKey(PRIVATE_KEY);

        let datakey = "user"
        let dataString = `abcd1234@@&90`;//要加密的字符串
        let encryptedStr = encryptor.encrypt(dataString);
        console.log("加密后:", JSON.stringify(encryptedStr));

        cc.sys.localStorage.setItem(datakey, encryptedStr);

        let cipherText = cc.sys.localStorage.getItem(datakey);//要解密的文本
        let dataString2 = decryptor.decrypt(cipherText);//得到解密之后的文本
        console.log("解密后:", JSON.stringify(dataString2));
    }
}


```

运行效果

![image-20200904172601915](Cocos-Creator使用jsencrypt本地存储加密-RSA/image-20200904172601915.png)

### 总结

步骤稍显复杂和对称加密相比，优点是安全性更好些，缺点是速度比较慢，不适合快速大量的存储数据，那么有没有更好的方法呢？答案是有的，就是结合前面的对称加密方式，做一个混合加密的策略。

> 混合加密是结合 **对称加密** 和 **非对称加密** 各自优点的一种加密方式。其具体的实现思路是先使用 **对称加密算法** 对数据进行加密，然后使用非对称加密算法对 **对称加密的密钥** 进行非对称加密，之后再把加密后的密钥和加密后的数据发送给接收方。

### 参考

- [玩转混合加密](https://segmentfault.com/a/1190000023328344)
- [使用jsencrypt实现参数的前端加密](https://www.jianshu.com/p/3da3d81e1572)
- [jsencrypt加密和解密的那点事](https://www.jianshu.com/p/ccf6d3f6be1a)
- [CocosCreator中使用RSA公钥加密](https://segmentfault.com/a/1190000015799854)

