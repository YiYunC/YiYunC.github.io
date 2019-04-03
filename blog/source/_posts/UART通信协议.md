---
title: UART基础知识和裸机编程
date: 2018-08-16 07:34:23
tags:
  - 裸机
  - UART
---

&emsp;&emsp;嵌入式里常说的串口一般指的是UART，4-pin杜邦头(VCC,GND,RX,TX),使用的是TTL/CMOS电平。
而COM口就是电脑上常用的那种9-pin插头，使用的是RS-232电平。

&emsp;&emsp;UART，即Universal Asynchronous Receiver and Transmitter,通用异步收发器,包括了TTL、CMOS、RS-232、RS-422和RS-485等接口标准规范和总线标准规范，它规定了通信口的电气特性、传输速率、连接特性和接口的机械特性等内容，这些标准规范属于物理层的概念。
UART通信协议，属于通信网络中的数据链路层概念。


<!--more-->

# 1.电平标准 #
&emsp;&emsp;首先要知道我们常用的电平TTL和RS-232的区别，可以从下图传输'C'的波形图观察两者的差异。
![](https://raw.githubusercontent.com/YiYunC/cloudimg/master/data/1.png)


| Tables    | Logical Level: 0 | Logical Level: 1 |
| --------- |:----------------:| ----------------:|
| TTL:    V |      0 ~ x       |      y ~ 5       |
| RS-232: V |     +3 ~ +12     |     -3 ~ -12     |

<font color="#dd0000">注: 1. TTL中x,y值根据电路的情况选定 ; 2. 需要注意的是RS-232和TTL逻辑电平刚好相反。 </font><br />

两者的一些详细对比可以去看看这篇文章["RS-232 vs. TTL Serial Communication"](https://www.sparkfun.com/tutorials/215)。
需要补充基础知识的就点开这个文章[“什么是TTL电平、CMOS电平”](http://www.21ic.com/jichuzhishi/analog/questions/2013-05-17/181650.html)。
至于RS-422、RS-485等，这两种电平抗干扰能力强，应用于长距离传输;RS-232一般用于短距离传输。

# 2.UART通信协议 #
&emsp;&emsp;