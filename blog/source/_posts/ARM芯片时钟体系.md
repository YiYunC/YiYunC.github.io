---
title: ARM芯片时钟体系
date: 2018-08-10 21:20:41
tags: 
  - 裸机
  - ARM时钟
  - S3C2440
cover: https://raw.githubusercontent.com/YiYunC/cloudimg/master/data/2a6a93c00e6.jpg
---
以前用STM32板子的时候，没有仔细看过ARM芯片的时钟体系，**“拿来主义”**贯彻到底，这次写篇文章来总结顺便复习下ARM芯片时钟体系的构成。
![](https://raw.githubusercontent.com/YiYunC/cloudimg/master/data/2a6a93c00e6.jpg)


<!--more-->

# 一、S3C2440 #
最近在学习嵌入式Linux开发，用的S3C2440芯片的板子，板子外接的晶振频率为12MHz，需要通过时钟控制逻辑的PLL（锁相环）提高系统时钟。

时钟控制逻辑提供了3种时钟：
1.FCLK用于CPU核。

2.HCLK用于AHB总线上的设备（频率相对较高），H可以理解为high，如CPU核、Memory CONT.、Interrupt CONT.、LCD控制器、DMA等。
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-11/3215543.jpg)

3.PCLK用于APB总线上的设备（频率相对较低），P可以理解为peripheral，如Watchdog、I2C、SPI、UART、SPI、GPIO等外设。
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-11/52372645.jpg)
----------

板子设有两个PLL：MPLL和UPLL。UPLL专用于USB设备，MPLL用来设置FCLK、HCLK、PCLK。

在芯片手册里可以查到FCLK、HCLK、PCLK最高分别为400、136、68MHz。那么如何通过编程把12M的时钟源提高成这些新的时钟是我们接下来分析的问题。

## 1.上电复位序列图 ##
在芯片手册可以找到一个**上电复位序列图**。
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-11/82972905.jpg)
仔细推敲这个图，会发现有几个特别的地方：1.刚上电，复位引脚nRESET不是马上拉到高电平；2.OM[3:2]的作用；3.Lock Time里的工作机制。

查看下原理图，会发现nRESET上连接的芯片是IMP811T，网上查找芯片资料
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-11/22689008.jpg)

所以等到板子电源稳定后复位引脚才会输出高电平。

![](http://pcwv6qdfo.bkt.clouddn.com/18-8-11/96230094.jpg)
OSC是**晶体振荡器**，EXTCLK是另一个引脚。而**OM[3:2]**其实就是一个选择器，在下图中可以看出它控制引脚OM3、OM2模式的状态。
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-11/73806757.jpg)
原理图里可以看到OM2和OM3引脚均**接地**，所以应该是00的模式，即选择晶振作为时钟源。

引脚OM[3:2]的电平在被内部电路捕获后，PLL就准备开始工作了，Lock Time就是PLL的锁存时间（有初始值，可以由相应寄存器设定），这段时间内，FCLK停振，CPU停止工作，MPLL开始工作；过了Lock Time这段时间后，FCLK=MPLL输出的新时钟，此时CPU重新运行。

- **小结：OM模式，Lock Time这两个是基础的设定。**

## 2.PLL寄存器的设定 ##
接下来重要的是PLL的设定，主要由CLKDIVN、CAMDIVN、MPLLCON这几个寄存器来设定（UPLLCON暂时不讲）。

CLKDIVN寄存器是用来设定FCLK、HCLK、PCLK这三者一些简单的比例关系，一些比较特殊的比例关系就要用到CAMDIVN寄存器。

MPLLCON寄存器是设定FCLK与**晶振频率（Fin）**的倍数，芯片手册有相应的公式：
{% codeblock lang:objc %}
Mpll = (2 * m * Fin) / (p * 2^S)
m = (MDIV + 8), p = (PDIV + 2), s = SDIV
{% endcodeblock %}

通过公式简单计算可以得到MDIV、PDIV、SDIV的值，再通过移位操作设定寄存器的值。
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-11/20349431.jpg)

## 3.CPU bus mode ##
芯片手册第七章看完，会看到一个NOTE:
![](http://pcwv6qdfo.bkt.clouddn.com/18-8-13/53274620.jpg)

这里说到当HDIVN不为0，必须执行以下指令将CPU bus mode更改成asynchronous bus mode（异步总线模式）：
{% codeblock lang:objc %}
MMU_SetAsyncBusMode
mrc p15,0,r0,c1,c0,0
orr r0,r0,#R1_nF:OR:R1_iA   //R1_nF:OR:R1_iA==0xc0000000
mcr p15,0,r0,c1,c0,0
{% endcodeblock %}

而当HDIVN不为0且CPU bus mode是fast bus mode（快速总线模式）时，CPU工作频率将变为HCLK，不再是FCLK。


下面以设置FCLK=400MHz，HCLK=400MHz，PCLK=400MHz为例子写出部分代码。

----------

{% codeblock lang:objc %}
   /* 设定Lock Time的值->初始值 */
   ldr r0, =0x4C000000    //address
   ldr r1, =0xFFFFFFFF    //Reset Value
   str r1, [r0]           

   /* 设定CPU总线模式->异步模式 */
   mrc p15,0,r0,c1,c0,0
   orr r0,r0,#0xc0000000  //R1_nF:OR:R1_iA
   mcr p15,0,r0,c1,c0,0

   /* 设定CLKDIVN的值-> FCLK : HCLK : PCLK = 8 : 4 : 1 */
   ldr r0, =0x4C000014    //address
   ldr r1, =0x5           //set Value: [3:0]=101
   str r1, [r0]           

   /* 设定CLKDIVN的值->设置其他频率比例的寄存器 */
   //ldr r0, =0x4C000018    //address
   //ldr r1, =0x           //set Value
   //str r1, [r0] 

   /* 设定MPLLCON的值输出PLL
    * Mpll = (2 * m * Fin) / (p * 2^S)
    * m = (MDIV + 8), p = (PDIV + 2), s = SDIV
    * 查表计算得MDIV = 92, PDIV = 1, SDIV = 1
    * Output Frequency = 400 MHz
    */
   ldr r0, =0x4C000004              //address
   ldr r1, =(92<<12)|(1<<4)|(1<<0)  //set Value
   str r1, [r0]

   /* 一旦设置PLL,就会锁定LOCK TIME直到PLL输出稳定
    * 然后CPU工作于新的频率FCLK
    */
{% endcodeblock %}



# 二、STM32F103 #
以前用的STM32板子芯片是STM32F103VET6,其时钟的配置全都可以调用固件库轻易完成。
我们可以看看STM32的官方手册里，有芯片时钟体系的整体框图，





----------

持续更新中..........





