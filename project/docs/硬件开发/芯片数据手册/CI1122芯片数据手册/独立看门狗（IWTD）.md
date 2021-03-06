# 独立看门狗（IWTD）

## 功能介绍

看门狗定时器是一种硬件定时电路，主要用于监测系统是否发生由软件工作异常而引发的故障。独立看门狗模块基于一个32-bit递减计数器，使用独立于PCLK的时钟计数，当计数器递减计数到0时，产生中断请求，计数器重载初值再次进行递减计数，再递减计数到0之前若中断未被清除，将产生复位请求，同时计数器停止计数。中断请求和复位请求都可以通过寄存器配置为使能或者禁止，当禁止中断请求时，计数器停止计数，当中断请求重新使能后，计数器重载初值进行递减计数。

软件配置时需向锁定寄存器中写入0x1ACCE551，才能访问相关的其余寄存器。

## 寄存器映射

IWTD寄存器映射的基地址为0x4003A000，具体的寄存器映射见表13。

<div align=center>表13 IWTD寄存器映射</div>

<center>

偏移量 | 名称 | 位宽 | 类型 | 复位值 | 描述
:--: | :--: | :--: | :--: | :--: | :--:
0x00 | WdogLoad | 32 | R/W | 0xFFFFFFFF | 计数初值寄存器
0x04 | WdogValue | 32 | RO | 0xFFFFFFFF | 计数值寄存器
0x08 | WdogControl | 32 | R/W | 0x00000000 | 控制寄存器
0x0C | WdogIntClr | 32 | WO | - | 中断清除寄存器
0x10 | WdogRIS | 32 | RO | 0x00000000 | 原始中断状态寄存器
0x14 | WdogMIS | 32 | RO | 0x00000000 | 屏蔽中断状态寄存器
0xC00 | WdogLock | 32 | R/W | 0x00000000 | 锁定寄存器

</center>

## 计数初值寄存器（WdogLoad）

偏移量：0x00

复位值：0xFFFFFFFF

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:0 | WdogLoad | 0xFFFFFFFF | R/W | 计数初值寄存器

</center>

## 计数值寄存器（WdogValue）

偏移量：0x04

复位值：0xFFFFFFFF

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:0 | WdogValue | 0xFFFFFFFF | R/W | 计数值寄存器

</center>

## 控制寄存器（WdogControl）

偏移量：0x08

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:2 | Reserved | 0x00000000 | R/W | 保留
1 | RESEN | 0 | R/W | 复位请求使能：<br>0：禁止<br>1：使能
0 | INTEN | 0 | R/W | 中断请求使能：<br>0：禁止<br>1：使能

</center>

## 中断清除寄存器（WdogIntClr）

偏移量：0x0C

复位值：-

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:0 | WdogIntClr | - | WO | 中断清除寄存器：<br>向此寄存器写入任何值可清除中断请求，计数器重载初值进行递减计数。

</center>

## 原始中断状态寄存器（WdogRIS）

偏移量：0x10

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:1 | Reserved | 0x00000000 | RO | 保留
0 | WdogRIS | 0 | RO | 原始中断状态

</center>

## 原始中断状态寄存器（WdogRIS）

偏移量：0x14

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:1 | Reserved | 0x00000000 | RO | 保留
0 | WdogMIS | 0 | RO | 屏蔽中断状态

</center>

## 锁定寄存器（WdogLock）

偏移量：0xC00

复位值：0x00000000

<center>

位域 | 名称 | 复位值 | 类型 | 描述
:--: | :--: | :--: | :--: | :--:
31:2 | WdogLock | 0x00000000 | R/W | 锁定寄存器：<br>向此寄存器写入0x1ACCE551才能写该模块相关其他所有的寄存器，否则不能写其他所有的寄存器。<br>读此寄存器时：<br>0x00000000：可以写其他所有寄存器<br>0x00000001：不能写其他所有寄存器

</center>