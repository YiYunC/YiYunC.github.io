---
title: 判断CPU的大小端字节序
date: 2018-08-06 11:11:23
tags: 
  - 随笔
  - 字节序
cover: https://raw.githubusercontent.com/YiYunC/cloudimg/master/data/2.jpg
---


**小端（Little endian)**：低字节存在内存的低地址中，高字节存在内存的高地址中。
**大端（Big endian）**：低字节存在内存的高地址中，高字节存在内存的低地址中。
![](https://raw.githubusercontent.com/YiYunC/cloudimg/master/data/2.jpg)
<!--more-->

# 主机序和网络字节序 #

>**两台主机之间通过TCP/IP协议进行通信的时候，在向对方发送报文前，都需要调用相应的函数把自己的主机序（Little endian）模式转换成网络序（Big endian）模式;同样，在接收到对方的报文信息后，都需要将网络序转换成主机序，即将报文从Big endian转换成Little endian**。

Little endian优点：强制转换数据型不需要再调整字节。
Big endian优点：有符号数，其字节最高位不仅表示数值本身，还起到了符号的作用。


{% codeblock lang:objc %}
#include <stdio.h>
int main()
{
  int a = 0x12345678;
  char *p;
  p = ( char *)&a;
  if (*p == 0x12)
  printf("Big endian\n");
  else
  printf("Little endian\n");
}   
{% endcodeblock %}

