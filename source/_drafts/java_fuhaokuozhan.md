---
title: Java补零扩展和补符号扩展
tags:
- Java
categories:
- Java
---

java中没有无符号类型，所以Java的类型转换中的扩展都是补符号扩展，当低长度的类型扩展至高长度类型时，如果是正数，因为符号位为0，则在高位补0；如果是负数，符号位为1，则需要在高位补1。
<!--more-->
例如，对于`byte b = 1;`,其二进制补码为**10000001**,当转换为**int**类型时，其二进制补码为**00000000 00000000 00000000 00000001**；
对于`byte b = -127;`,其二进制补码为**10000001**,当转换为**int**类型时，其二进制补码为**11111111 11111111 11111111 10000001**.  
对于普通的Java类型转换，符号位扩展并没有特别大的意义。但是对于下边这种情况，就非常有意义:
**对于`byte[] bytes = {-1, -2, -3, -4}`,转换成`short[]`,`short[0]`和`short[1]`分别为多少？**
这个问题的思路就是移位操作，在进行合并组合即可，代码如下：
```java
byte[] bytes = {-1, -2, -3, -4};
short[] shorts = new short[2];
shorts[0] = (short) ((bytes[0] & 0xFF) << 8 | (bytes[1] & 0xFF));
shorts[1] = (short) ((bytes[2] & 0xFF) << 8 | (bytes[3] & 0xFF));
System.out.println(shorts[0]);
System.out.println(shorts[1]);
```
这里最重要的一点儿就是`& 0xFF`,这么做的意义在什么地方？先看如下代码：
```java
byte b = -127;
System.out.println(b);
int i = b & 0xFF;
System.out.println(i);
```
代码执行结果如下：  
![](http://ok16gn2ql.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-09-10%2022.50.11.png)    
为什么会出现这种结果？下面我们来分析一下：
