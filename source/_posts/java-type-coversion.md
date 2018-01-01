---
title: Java补零扩展和补符号扩展
tags:
  - Java
categories:
  - Java
date: 2017-09-12 00:50:21
---


java中没有无符号类型，所以Java的类型转换中的扩展都是补符号扩展，当低长度的类型扩展至高长度类型时，如果是正数，因为符号位为0，则在高位补0；如果是负数，符号位为1，则需要在高位补1，这样才能保证不管何种类型，数字的值都是没有改变的。
<!--more-->
例如，对于`byte b = 1;`,其二进制补码为**10000001**,当转换为**int**类型时，其二进制补码为**00000000 00000000 00000000 00000001**；
对于`byte b = -127;`,其二进制补码为**10000001**,当转换为**int**类型时，其二进制补码为**11111111 11111111 11111111 10000001**.  
对于普通的Java类型转换，符号位扩展并没有特别大的意义。但是对于下边这种情况，就非常有意义:  
**对于`byte[] bytes = {-1, -2, -3, -4}`,转换成`short[]`,`short[0]`和`short[1]`分别为多少？**
这个问题的思路就是移位操作，在进行合并组合即可，代码如下：  
代码1：
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
**首先，需要明白的一点，对于操作数位数低于`int`类型的，在进行运算符计算的时候默认会自动转换为`int`类型再进行计算**  
那么对于`b & 0xFF`,变量b的二进制补码为：10000001,转化为`int`类型二进制补码为：11111111 11111111 11111111 10000001，0xFF的二进制补码为：11111111。那么实际运算过程为`11111111 11111111 11111111 10000001 & 11111111`,结果为：0000000 00000000 00000000 10000001，而不是-127的二进制补码，实际值是`int`类型的129；  

我们接着分析代码1,先看一下代码执行结果：
![](http://ok16gn2ql.bkt.clouddn.com/%E5%B1%8F%E5%B9%95%E5%BF%AB%E7%85%A7%202017-09-11%2021.47.29.png)  
我们看一下主要代码：  
`bytes[0] = -1`二进制补码为：11111111
`bytes[1] = -2`的二进制补码为：11111110
如果代码不执行`bytes[0] & 0xFF`（结果为：00000000 00000000 00000000 11111111）而是直接进行`bytes[0] << 8`（结果为：11111111111111111111111100000000）运算，那么`(bytes[0] & 0xFF) << 8 | (bytes[1] & 0xFF)`计算就会变成11111111111111111111111100000000|11111111111111111111111111111110,这样我们会发现，这已经不是我们想要的计算结果了，这是因为符号位扩展导致的。我们实际想要的计算过程是00000000000000001111111100000000|00000000000000000000000011111110。而这可以通过`& 0xFF`,`bytes[0]`通过和00000000000000000000000011111111（即0xFF）进行`&`运算获得；这就是为什么我们要在计算的时候执行`& 0xFF`运算；

**最后：** 对于代码计算中，经常会碰到这种需要把低位类型合并高位类型进行位运算的情况，由于java类型扩展是补符号位扩展，那么就会导致有时在进行位运算时经过类型扩展的二进制补码并不是原补码，导致计算错误，所以就可以通过`&0xFF`这种形式的计算，只保留原来正确的补码位数来进行计算，保证计算的正确性。